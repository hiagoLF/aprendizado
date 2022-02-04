# Repositórios Para Estudos e Anotações

# Estudos em SQL

### Criando container MariaDB com docker

```bash
❯ docker run -d --name mariadb-server -p 3306:3306 -e "MYSQL_ROOT_PASSWORD=root" mariadb
```

### Basic

```sql
-- Criar banco de dados
create database cadastro

show databases;

-- Fazer operações no banco cadastro
use cadastro;

create table pessoas (
	nome varchar(30),
	idade tinyint(3),
	sexo char(1),
	peso float,
	altura float,
	nacionalidade varchar(20)
);

-- Mostrar extrutura da tabela
describe pessoas;

-- Remover tabela
drop table pessoas;

-- Remover banco de dados por completo
drop database cadastro ;

-- Criar tabela com padrão de caractere utf8
create database cadastro
default character set utf8
default collate utf8_general_ci;

-- Criando Tabela de pessoas
use cadastro;
create table pessoas (
	id int not null auto_increment,
	nome varchar(30) not null,
	nascimento date,
	sexo enum('M', 'F'),
-- 	Cinco números decimais sendo 2 depois da vírgula
	peso decimal(5,2),
	altura decimal(3,2),
	nacionalidade varchar(20) default 'Brasil',

	PRIMARY KEY (id)
) default charset = utf8;

-- Inserindo dados na tabela
-- Caso a ordem dos valores inseridos seja a mesma da tablela, não é necessário passar o nome dos campos
insert into pessoas
(id, nome, nascimento, sexo, peso, altura, nacionalidade)
values
(default, 'Bambuu', '1999-12-04', 'M', '80.0', '1.70', default);

select * from pessoas

-- Modificando tabela
alter table pessoas
add column profissao varchar(10);

alter table pessoas
drop column profissao;

-- Adicionar coluna na primeira posição
alter table pessoas
add column codigo int first;

alter table pessoas
modify column profissao varchar(20) not null default '';

-- Alterando tudo inclusive o nome da coluna: profissao -> prof
alter table pessoas
change column profissao prof varchar(20) not null default '';

-- Renomear o nome da tabela
alter table pessoas
rename to gafanhotos;

create table if not exists cursos (
	nome varchar(30) not null unique,
	descricao text,
-- 	unsigned -> Sem sinal. Não pode ser negativo
	carga int unsigned,
	totaulas int unsigned,
	ano year default '2016'
) default charset = utf8;

alter table cursos
modify column idcurso int auto_increment first;

alter table cursos
add primary key (idcurso);

insert into cursos values
(default ,"Java", "Só aprende a parada aí doido", '10', '50', default),
(default ,"HTL5", "Só aprende a parada aí doido", '10', '50', default),
(default ,"JS", "Só aprende a parada aí doido", '10', '50', default),
(default ,"React", "Só aprende a parada aí doido", '10', '50', default),
(default ,"Node", "Só aprende a parada aí doido", '10', '50', default),
(default ,"Nest", "Só aprende a parada aí doido", '10', '50', default),
(default ,"MariaDB", "Só aprende a parada aí doido", '10', '50', default),
(default ,"MongoDb", "Só aprende a parada aí doido", '10', '50', default);

-- Alterando linha
update cursos
set descricao = 'Esse curso de Java é legar parceru, cofia...', carga = '23'
where idcurso = '1'

-- Alterando linha
-- Apenas uma linha poderá ser afetada. Ou remova o limit
update cursos
set totaulas = '10'
where carga  = '10';
limit 1;

delete from cursos
where idcurso  = '3';

-- Remover todas as linhas de cursos sem remover a tabela
truncate cursos;
```

### Select

```sql
select * from cursos
order by carga;

select * from cursos
order by carga desc;

select nome, descricao from cursos
order by carga desc;

select nome, descricao from cursos
where carga = '10'
order by carga desc;

select * from cursos
where carga < '100'
order by carga asc, nome asc;

select * from cursos
where carga BETWEEN 40 AND 200;

select * from cursos
where carga in ('3', '5');
```

### Relations
```sql
use cadastro;

alter table gafanhotos add cursopreferido int;

describe gafanhotos 

-- Criar relação, colocando chave estrangeira de cursos em gafanhotos
-- idcurso vai ser armazenado em cursopreferido 
alter table gafanhotos
add foreign key (cursopreferido)
references cursos(idcurso);

-- Relacionando linhas de tabelas diferentes
update gafanhotos set cursopreferido = '3' where id = '7';

-- Fazendo união na consulta
select * from gafanhotos join cursos
on gafanhotos.cursopreferido = cursos.idcurso;

-- Pegando campos específicos no join
-- inner join é semelhante a join sozinho
select gafanhotos.nome, cursos.nome from gafanhotos inner join cursos
on gafanhotos.cursopreferido = cursos.idcurso;

-- Alias
select g.nome, c.nome
from gafanhotos as g inner join cursos as c
on g.cursopreferido = c.idcurso;

-- Trazer os resultados independente de haver relação ou não
select g.nome, c.nome
from gafanhotos as g left outer join cursos as c
on g.cursopreferido = c.idcurso;

-- Criação de tabela de relação Muitos para Muitos
create table gafanhoto_assiste_curso(
	id int not null auto_increment,
	data date,
	idgafanhoto int,
	idcurso int,
	primary key (id),
	foreign key (idgafanhoto) references gafanhotos (id),
	foreign key (idcurso) references cursos (idcurso)
) default charset = utf8;

insert into gafanhoto_assiste_curso values
(default, '2014-03-01', '1', '2'),
(default, '2014-03-01', '3', '1'),
(default, '2014-03-01', '4', '5'),
(default, '2014-03-01', '5', '3'),
(default, '2014-03-01', '3', '5'),
(default, '2014-03-01', '2', '3'),
(default, '2014-03-01', '1', '5');


select * from gafanhoto_assiste_curso;

-- Select em relação M-M
select g.nome pessoa, c.nome curso from gafanhotos g
join gafanhoto_assiste_curso a
on g.id = a.idgafanhoto
join cursos c
on c.idcurso = a.idcurso;
```
