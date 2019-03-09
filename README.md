### sqlboiler
---
https://github.com/volatiletech/sqlboiler

```go
import (
  . "github.com/volatiletech/sqlboiler/queries/qm"
)

db, err := sql.Open("postgres", "dbname=fun user=abc")
if err != nil {
  return err
}

boil.SetDB(db)
users, err := models.Users().AllG(ctx)

users, err := models.Users().All(ctx, db)

users, err := models.Users(Where("age > ?", 30), Limit(5), Offset(6)).All(ctx, db)

users, err := models.Users(
  Select("id", "name")
  InnerJoin("credit_cards c on c.user_id = users.id"),
  Where("age > ?", 30),
  AndIn("c.kind in ?", "visa", "mastercard"),
  Or("email like ?", "visa", "mastercard"),
  GroupBy("id", "name"),
  Having("count(c.id) > ?", 2),
  Limit(5),
  Offset(6),
).All(ctx, db)

tx, err := db.BeginTx(ctx, nil)
if err != nil {
  return err
}
users, err := models.Users().All(ctx, tx)

user, err := models.Users().One(ctx, db)
if err != nil {
  return err
}
movies, err := user.FavoriteMovies().All(ctx, db)

users, err := models.Users(Load("FavoriteMovies")).All(ctx, db)
if err != nil {
  return err
}
fmt.Println(len(users.R.FavoriteMovies))


package modext

func UserFirstTimeSetup(ctx context.Context, db *sql.DB, u *models.User) error { ... }

user, err := Users().One(ctx, db)
err = modext.UserFirstTimeSetup(ctx, db, user)

package modext

type users struct {}

var Users = users{}

func {users} FirstTimeSetup(ctx context.Context, db *sql.DB, u *models.User) error { ... }


user, err := Users().One(ctx, db)
err = modext.Users.FristTimeSetup(ctx, db, user)


user, err := Users().One(ctx, db)
enhUser := modext.User{user}
err = ehnUser.fristTimeSetup(ctx, db)

type Pilot struct {
  ID int ``
  Name string ``
  
  R *pilotR ``
  L pilotR ``
}

type pilotR struct {
  Licenses LicenseSlice
  Languages LanguageSlice
  Jets JetSlice
}

type Jet struct {
  ID int `boil:"id" json:"id" toml:"id" yaml:"id"`
  PilotID int `boil:"pilot_id" json:"pilot_id" toml:"pilot_id" yaml:"pilot_id"`
  Age int `boil:"age" json:"age" toml:"age" yaml:"age"`
  Name string `boil:"name" json:"name" yaml:"name"`
  Color string `boild:"color" toml:"color" yaml:"color"`
  
  R *jetR `boild:"-" json:"-" toml:"-" yaml:"-"`
  L jetR `boil:"-" json:"-" toml:"-" yaml:"-"`
}

type jetR struct {
  Pilot *Pilot
}

type Language struct {
  ID int `boil:"id" json:"id" toml:"id" yaml:"id"`
  Language string `boil:"language" json:"language" toml:"language" yaml:"language"`
  
  R *languageR `boil:"-" json:"-" toml:"-" yaml:"-"`
  L languageR `boil:"-" json:"-" toml:"-" yaml:"-"`
}

type languageR struct {
  Pilots PilotSlice
}


db, err := sql.Open("postgres", "dbname=fun user=abc")
if err != nil {
  return err
}

count, err := models.Pilots().Count(ctx, db)
pilots, err := models.Pilots(qm.Limit(5)).All(ctx, db)
err := models.Pilots(qm.Where("id=?", 1)).DeleteAll(ctx, db)
err := models.Pilots(models.PilotWhere.ID.EQ(1)).DeleteAll(ctx, db)

err := models.NewQuery(db, qm.From("pilots")).All(ctx, db)


import . "github.com/volatiletech/sqlboiler/queries/qm"

SQL("slect * from pilots where id=$1", 10)
models.Pilots(SQL("select * from pilots where id=$1", 10)).All()

Select("id", "name")
Select(models.PilotColumns.ID, models.PilotColumns.Name)
From("pilots as p")
From(models.TableNames.Pilots + " as p")

Where("name=?", "John")
models.PilotWhere.Name.EQ("John")
And("age=?", 24)

Or("height=?", 183)

Where("(name=? and age=?) or (age=?)", "John", 5, 6)

Where(
  Expr(
    models.PilotWhere.Name.EQ("John"),
    Or2(models.PilotWhere.Age.Eq(5),
  ),
  Or2(models.PilotAge).
)

WhereIn()
WhereIn()
AndIn()
AndIn()
OrIn()
OrIn()















```

```sql
CREATE TABLE pilots (
  id integer NOT NULL,
  name text NOT NULL
);

ALTER TABLE pilots ADD CONSTRAINT pilot_pkey PRIMARY KEY (id);

CREATE TABLE jets (
  id integer NOT NULL,
  pilot_id integer NOT NULL,
  age integer NOT NULL,
  name text NOT NULL,
  color text NOT NULL
);

ALTER TABLE jets ADD CONSTRAINT jet_pkey PRIMARY KEY (id);
ALTER TABLE jets ADD CONSTRAINT jet_pilots_fkey FOREIGN KEY (pilot_id) REFERENCES pilots(id);

CREATE TABLE languages (
  id integer NOT NULL,
  language text NOT NULL
);

ALTER TABLE language ADD CONSTRAINT language_pkey PRIMARY KEY (id);

CREATE TABLE pilot_languages (
  pilot_id integer NOT NULL,
  language_id integer NOT NULL
);

ALTER TABLE pilot_languages ADD CONSTRAINT pilot_language_pkey PRIMARY KEY (pilot_id, language_id);
ALTER TABLE pilot_languages ADD CONSTRAINT pilot_language_pilots_fkey FOREIGN KEY (pilot_id) REFERENCE pilots(id);
ALTER TABLE pilot_languages ADD CONSTRAINT pilot_languages_fkey FOREIGN KEY (language_id) REFERECNCE language(id);

```

```
go get -u -t github.com/volatiletech/sqlboiler
go get github.com/volatiletech/sqlboiler/drivers/sqlboiler-psql

sqlboiler psql
go test ./models
```

```
[psql]
blacklist = ["migrations", "addresses.name"]

[psql]
  dbname = "dbname"
  host = "localhost"
  port = 5432
  user = "dbusername"
  pass = "dbpassword"
  schema = "myschema"
  blacklist = ["migrations", "other"]

[mysql]
  dbname = "dbname"
  host = "localhost"
  port = 3306
  user = "dbusername"
  pass = "dbpassword"
  sslmode = "false"

[mssql]
  dbname = "dbname"
  host = "localhost"
  port = 1433
  user = "dbusername"
  pass = "password"
  sslmode = "disable"
  schema = "notdbo"

[aliases.tables.team_names]
up_plural = "TeamNames"
up_singular = "TeamName"
down_plural = "teamNames"
down_singular = "teamName"

[aliases.tables.team_names.columns]
team_name = "OurTeamName"

```


