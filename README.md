# Database Systems — Movie and TV Series Monitoring Application

> Academic project developed for the Database Systems (BD) course in the Bachelor's in Informatics and Computing Engineering (LEIC) at the Faculty of Engineering, University of Porto (FEUP), academic year 2024/2025.

## Overview

This repository contains the database model and SQL implementation for an audiovisual content monitoring platform. Inspired by applications such as Queue, Letterboxd, and TV Time, the system allows users to track movies, TV series, seasons, and episodes, record viewing activity and progress, maintain custom watchlists, interact socially with other users, and view streaming platform availability and cast details.

## Authors

Group 1303 (LEIC 2024/2025):
- Catarina Bastos (up202307631@fe.up.pt)
- Nuno Costa (up202305503@fe.up.pt)
- Vasco Gonçalves (up202305513@fe.up.pt)

The official project report submitted for evaluation is available in [Relatório.pdf](Relatório.pdf).

## Domain Model and UML Diagram

The domain model was designed using UML notation to represent entity attributes, relationships, multiplicities, and constraints.

![UML Class Diagram](Projeto.png)

### Core Entities

- **Utilizador (User):** Stores account details, profile info, and social metrics (followers, following, points).
- **Item:** Generic base entity for media content, specialized into **Filme** (Movie) and **Serie** (TV Series).
- **Filme:** Specifies release date, runtime, filming location, and language.
- **Serie:** Aggregates seasons and overall broadcast dates.
- **Temporada (Season):** Groups episodes within a TV series.
- **Episodio (Episode):** Individual episode belonging to a season, with runtime, release date, and age rating.
- **Atividade (Activity):** User interaction log (rating from 0 to 10, completion progress percentage, start/end dates, review).
- **Lista & Adicao:** User-created lists (public or private) and timestamped items added to lists.
- **Participante & Participacao:** Actors, directors, and crew members along with their project participation.
- **Plataforma:** Streaming services and associated monthly costs.
- **FaixaEtaria:** Recommended age rating limits.

## Relational Schema and Normalization

### Relational Schema

- **Utilizador** (id, nome, bio, genero, dataNascimento, dataEntrada, nSeguidores, nPontos, nSeguindo, email, uPassword)
- **Seguir** (idSegue -> Utilizador, idSeguido -> Utilizador)
- **FaixaEtaria** (id, idadeInicio, idadeFim)
- **Item** (id, nome, tipo, descricao, classificacao, idFaixaEtaria -> FaixaEtaria)
- **Filme** (idItem -> Item, dataLancamento, duracao, localFilmagem, linguagem)
- **Serie** (idItem -> Item, dataInicio, dataFim, nTemporadas)
- **Temporada** (id, numero, nEpisodios, descricao, idSerie -> Serie)
- **Episodio** (id, nome, numero, dataLancamento, duracao, descricao, idFaixaEtaria -> FaixaEtaria, localFilmagem, linguagem, idTemporada -> Temporada)
- **Lista** (id, nome, descricao, tipoLista, numItens, idUtilizador -> Utilizador, nFavoritos)
- **Adicao** (idLista -> Lista, idItem -> Item, dataAdicao)
- **Favorito** (idLista -> Lista, idUtilizador -> Utilizador)
- **Categoria** (id, nome)
- **ItemCategoria** (idCategoria -> Categoria, idItem -> Item)
- **Plataforma** (id, nome, custo)
- **ItemPlataforma** (idPlataforma -> Plataforma, idItem -> Item)
- **Participante** (id, nome, dataNascimento, genero, biografia, profissao, nProjetos)
- **Participacao** (idParticipante -> Participante, idItem -> Item)
- **Atividade** (idUtilizador -> Utilizador, idItem -> Item, rating, dataInicio, dataFim, progresso, critica)

### Normalization (3NF & BCNF)

As analyzed in the project report:
- **Third Normal Form (3NF):** Every functional dependency X -> A satisfies the condition that X is a superkey or A is a prime attribute. No transitive dependencies exist among non-prime attributes.
- **Boyce-Codd Normal Form (BCNF):** For every non-trivial functional dependency X -> A, the left side X is a superkey.

All relations in the database satisfy both 3NF and BCNF requirements, ensuring data integrity and avoiding redundancy or update anomalies.

## Project Evolution (Stage 1 vs. Stage 2)

The implementation evolved across two main iterations:
1. **Stage 1 (`create1.sql` / `populate1.sql`):** Defined the initial relational schema and populated basic test data.
2. **Stage 2 (`create2.sql` / `populate2.sql`):** 
   - Introduced foreign key cascade policies (`ON DELETE CASCADE`, `ON DELETE SET NULL`, `ON UPDATE CASCADE`) to maintain referential integrity automatically (e.g., deleting a season automatically removes its episodes).
   - Added `CHECK` constraints for domain integrity (e.g., release dates cannot be in the future, progress must be between 1% and 100%, runtimes must be positive, text lengths restricted).
   - Expanded dataset population in `populate2.sql`.

## Role of Artificial Intelligence

Artificial intelligence tools (ChatGPT) were used as an aid during development for:
- Exploring naming conventions and semantic improvements (such as renaming the `Assistiu` relation to `Atividade` and refining participant roles).
- Formulating SQLite `CHECK` constraint syntax for date and progress validation.
- Generating synthetic population data for `populate2.sql`.

All suggestions produced by AI were reviewed, adapted, and validated by the team according to the theoretical requirements of the course.

## Repository Structure

```
.
├── Projeto.png        # UML class diagram
├── Relatório.pdf      # Course project report (in Portuguese)
├── README.md          # Project documentation (in English)
└── sql/
    ├── create1.sql    # Stage 1 schema creation
    ├── populate1.sql  # Stage 1 data insertion
    ├── create2.sql    # Stage 2 refined schema with constraints
    ├── populate2.sql  # Stage 2 expanded data insertion
    └── run.sh         # Bash script to run SQLite database setup
```

## How to Run

### Requirements
- SQLite 3 command-line utility

### Running the Database

1. Open a terminal and navigate to the `sql` directory:
   ```bash
   cd sql
   ```

2. Load the schema and populate the database using `sqlite3`:
   ```bash
   sqlite3 projdb.db < create2.sql
   sqlite3 projdb.db < populate2.sql
   ```

3. Alternatively, execute the provided script:
   ```bash
   bash run.sh
   ```

4. You can then open and query `projdb.db` via CLI (`sqlite3 projdb.db`) or using a graphical interface such as DB Browser for SQLite.
