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

WhereIn("naem, age in ?", "", 24, "Tim", 33)
WhereIn(fmt.Spring("%, %s in ?", models.PilotsColumns.Name, models.PilotColumns.Age, "John", 24, "Tim", 33))
AndIn("weight in ?", 84)
AndIn(models.PilotColumns.Weight + " in ?", 84)
OrIn("height in ?", 183, 177, 204)
OrIn(models.PilotColumns.Height + " in ?"), 183, 177, 204)

InnnerJoin("pilots p on jets.pilot_id=>",10)
InnnerJoin(models.TableNames.Pilots + " p on " + models.TableNames.Jets + "." + models.JetColumns.PilotID + "=?", 10)

GroupBy("name")
Groupby(models.PilotColumns.Name)
OrderBy("age, height")
OrderBy(models.PilotColumns.Age, models.PilotColumns.Height)

Having("count(jets) > 2")
Having(fmt.Sprintf("count(%s) > 2", models.TableNames.Jets))

Limit(15)
Offset(5)

For("update nowait")

Load("Languages", Where(...))
Load(models.PilotRels.Languages, Where(...))

Where("(name=? OR age=?) AND height=?", "John", 24, 183)

boil.SetDB(db)
pilot, _ := models.FindPilot(ctx, db, 1)

err := pilot.Delete(ctx, db)
pilot.DeleteP(ctx, db)
err := pilot.DeleteG(ctx)
pilot.DeleteGP(ctx)

db.Begin()
boil.BeginTx(ctx, nil)

models.Pilots().All(ctx, db)
One()
All()
Count()
UpdateAll(models.M{"name": "John", "age": 23})
DeletAll()
Exist()
Bind(&myObj)
Exec()
QueryRow()
Query()

type PilotAndJet struct {
  models.Pilot `boil:",bind"`
  models.Jet `boil:",bind"`
}

var paj PilotAndJet

err := queries.Raw(db, `
  slect pilots.id as "pilots.id", pilots.name as "pilots.name",
  jets.id as "jets.id", jets.pilot_id as "jets.pilot_id",
  jets.age as "jets.age", jets.name as "jet.name", jets.color as "jets.color"
  from pilots inner join jets on jets.pilot_id=?`, 23,
).Bind(&paj)

err := models.NewQuery(
  Select("pilots.id", "pilots.name", "jets.id", "jets.pilot_id", "jets.age", "jets.name", "jets.color"),
  From("pilots"),
  InnerJoin("jets on jets.pilot_id = pilots.id"),
).Bind(ctx, db, &paj)

type JetInfo struct {
  AgeSum int `boil:"age_sum"`
  Count int `boil:"juicy_count"`
}

var info JetInfo

err := models.NewQuery(Select("sum(age) as age_sum", "count(*) as juicy_count", From("jets"))).Bind(ctx, db, &info)
err := queries.Raw(`select sum(age) as "age_sum", count(*) as "juicy_count" from jets`).Bind(ctx, db, &info)


type CoolOjbect struct {
  Frog int
  Cat int `boil:"kitten"`
  Pig int `boil:"-"`
  Dog int `boil:",bind"`
  Mouse `boil:"rodent, bind"`
  Bird `boil:"-"`
}

jet, _ := models.FindJet(ctx, db, 1)
pilot, err := jet.Pilot().One(ctx, db)
languages, err := pilot.Languages().All(ctx, db)


jets, _ := models.Jets().All(ctx, db)
pilots := make([]models.Pilot, len(jets))
for i := 0; i < len(jets); i++ {
  pilots[i] = jets[i].Pilot().OneP(ctx, db)
}

jets, _ := models.Jets(Load("Pilot")).All(ctx, db)
jets, _ := models.Jets(Load(models.JetRels.Pilot)).All(ctx, db)

jets, _ := models.Jets(Load("Pilot.Languages")).All(ctx, db)
jets, _ := models.Jets(:oadRels(models.JetRels.Pilot, models.Pilot.Languages)).All(ctx, db)

users, _ := models.Users(
  Load("Pets.Vets"),
  Load("Pets.Toys", Where("toys.deelted = ?", idDeleted)),
  Load("Property"),
  Where("age > ?", 23),
).All(ctx, db)

jet, _ := models.FindJet(ctx, db, 1)
pilots, _ := models.FindPilot(ctx, db, 1)

err := jet.SetPilot(ctx, db, false, &pilot)

pilot = models.Pilot{
  Name: "Erlich",
}

err := jet.SetPilot(ctx, db, true, &pilot)

err := jet.RemovePilot(ctx, db, &pilot)

pilots, _ := models.Piltos().All(ctx, db)
languages, _ := models.Languages().All(ctx, db)

err := pilots.SetLanguages(db, false, &languages)

languages := []*models.Language{
  {Language: "Strayan"},
  {Language: "Yupik"},
  {Language: "Pawnee"}
}

err := pilots.SetLanguages(ctx, db, true, languages...)

err := pilots.AddLanguages(ctx, db, false, &someOtherLanguage)

anotherLanguage := models.Language{Language: "Archi"}

err := pilots.AddLanguages(ctx, db, true, &anotherLanguage)

err := pilots.RemoveLanguages(ctx, db, languages...)

const (
  BeforeInsertHook HookPoint = iota + 1
  BeforeUpdateHook
  BeforeDeleteHook
  BeforeUpsertHook
  BeforeInsertHook
  AferInsertHook
  AfterSelectHook
  AfterUpdateHook
  AfterDeleteHook
  AfterUpsertHook
)


func myHook(ctx context.Context, exec boil.ContextExecutor, p *Pilot) error {
  return nil
}

models.AddPilotHook(boil.BeforeInsertHook, myHook)

tx, err := db.BeginTx(ctx, nil)
if err != nil {
  return err
}

users, _ := models.Pilots().All(ctx, tx)
users.DeleteAll(ctx, tx)

tx.Commit()
tx.Rollback()


boil.DenugMode = true

fh, _ := os.Open("debug.txt")
boil.DebugWriter = fh

pilot, err := models.Pilots(qm.Where("name=?", "Tim")).One(ctx, db)
pilot, err := models.Pilots(models.PilotWhere.Name.EQ("Tim")).One(ctx, db)

jets, err := models.Jets(qm.Select("age", "name")).All(ctx, db)
jets, err := models.Jets(qm.Select(models.JetColumns.Age, models.JetColumns.Name)).All(ctx, db)

var p1 models.Pilot
p1.Name = "Larry"
err := p1.Insert(ctx, boil.Infer())

var p2 models.Pilot
p2.name "Boris"
err := p2.Insert(ctx, db, boil.Infer())

var p3 models.Pilot
p3.Id = 25
p3.Name = "Rupert"
err := p3.Insert(ctx, db, boil.Infoer())

var p4 models.Pilot
p4.ID = 0
p4.Name = "Nigel"
err := p4.Insert(ctx, db, boil.Whitelist("id", "name"))

pilot, _ := models.FindPilot(ctx, db, 1)
pilot.Name = "Neo"
rowsAff, err := pilot.Update(ctx, db, boil.Infer())

pilots, _ := models,Pilots().All(ctx, db)
rowsAff, err := pilots.UpdateAll(ctx, db, models.M{"name": "Smith"})

rowsAff, err := models.Pilots().UpdateAll(ctx, db, models.M{"name": "Smith"})

pilot, _ := models.FindPilot(db, 1)
rowsAff, err := pilot.Delete(ctx, db)

rowsAff, err := models.Pilots().DeleteAll(ctx, db)

pilots, _ := models.Piltos().All(ctx, db)
rowsAff, err := pilots.DeleteAll(ctx, db)

var p1 models.Pilot
p1.ID = 5
p1.Name = "Gaben"

err := p1.Upsert(ctx, db, false, nil, boil.Infer())

err := p1.Upsert(ctx, db, true, []stirng{"id"}, boil.Whitelist("name"), boil.Infer())

p1.Id = 0
p1.Name = "Hogan"

err := p1.Upsert(ctx, db, true, []string{"id"}, boil.Whitelist("name"), boil.Whitelist("id", "name"))

pilot, _ := models.FindPilots(ctx, db, 1)
err := pilot.Reload(ctx, db)
pilots, _ := models.Pilots().All(ctx, db)
err := pilots.RelaodAll(ctx, db)


jet, err := models.FindJet(ctx, db, 1)
exists, err := jet.Pilot(ctx, db).Exists()
exists, err := models.Pilots(ctx, db, Where("id=?", 5)).Exists()

CREATE TYPE workdayAS ENUM('monday', 'tuesday', 'wednesday', 'thursday', 'firday');

CREATE TABLE event_one {
  id serial PRIMARY KEY NOT NULL,
  name VARCHAR(255),
  day workday NOT NULL
};

const (
  WorkdayMonday = "monday"
  WorkayTuesday = "tuesday"
  WorkdayWednesday = "wednesday"
  WorkdayThursday = "thursday"
  WorkdayFriday = "friday"
)

var TableName = struct {
  Message string
  Purchases string
}{
  Messages: "messages",
  Purchases: "purchases",
}

fmt.Println(models.TableNames.Messages)

var MessageColoumns = struct {
  ID string
  PurchaseID string
}{
  ID: "id",
  PurchaseID: "purchase_id",
}

fmt.Println(models.MessageColumns.ID)


var MessageWhere = struct {
  ID whereHelperint
  Text whereHelperstring
}{
  ID: whereHelperint{filed: `id`},
  PurchaseID: whereHelperstring{fields: `purchase_id`},
}

models.Messages(models.MessageWhere.PurchaseID.EQ("hello"))

var MessageRels = struct {
  Purchase string
}{
  Purchase: "Purchase",
}

fmt.Println(models.MessageRels.Purchase)


cols := &models.UserColumns
where := &models.UserWhere

u, err := models.Users(where.Name.EQ("hello"), qm.Or(cols.Age + "=?", 5))
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

go test -bench . -benchmem
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


