# apbd-TEST2-git-s34457

# README — Notatka do kolokwium 2

## Podejście: DatabaseFirst (max 75% ale pewnie)

---

## Krok 1 — Tworzenie projektu w Rider

New Solution → ASP.NET Core Web API:
- ✅ UseControllers
- ✅ NoHttps
- Reszta bez zmian

Usuń od razu:
- `WeatherForecast.cs`
- `WeatherForecastController.cs`

Utwórz foldery:
```
Controllers/ DTOs/ Data/ Exceptions/ Models/ Services/
```

---

## Krok 2 — NuGet

⚠️ Najpierw przejdź do folderu projektu:
```bash
cd NazwaProjektu
```

```bash
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

---

## Krok 3 — Baza danych w SSMS

1. Włącz VPN uczelniany
2. Połącz się z `db-mssql`
3. Uruchom skrypt SQL z zadania

---

## Krok 4 — Scaffold

⚠️ To najważniejsza komenda — generuje wszystkie modele automatycznie. Wypisuj tylko tabele z zadania:

```bash
dotnet ef dbcontext scaffold "Data Source=db-mssql;Initial Catalog=2019SBD;Integrated Security=True;Trust Server Certificate=True" Microsoft.EntityFrameworkCore.SqlServer --output-dir Models --context-dir Data --context DatabaseContext --force --table s34457.Tabela1 --table s34457.Tabela2
```
Przykladowa
```bash
dotnet ef dbcontext scaffold "Data Source=db-mssql;Initial Catalog=2019SBD;Integrated Security=True;Trust Server Certificate=True" Microsoft.EntityFrameworkCore.SqlServer --output-dir Models --context-dir Data --context DatabaseContext --force --table s34457.Client --table s34457.Status --table s34457.Order --table s34457.Product --table s34457.Product_Order
```

Po scaffold w `Models/` pojawią się wszystkie klasy, w `Data/` pojawi się `DatabaseContext.cs`.

---

## Krok 5 — DatabaseContext.cs

Usuń metodę `OnConfiguring` w całości — jest wygenerowana ale nie potrzebna.

---

## Krok 6 — appsettings.json

Wklej plik z repozytorium — zmień tylko connection string jeśli potrzeba.

---

## Krok 7 — Exceptions

Wklej bez zmian z repozytorium:
- `NotFoundException.cs` — zmień tylko namespace
- `ConflictException.cs` — zmień tylko namespace

---

## Krok 8 — DTOs

Patrz na JSON z zadania i twórz klasy. Zasada:

| JSON | C# |
|------|----|
| liczba bez kropki | `int` |
| liczba z kropką | `decimal` |
| tekst | `string` |
| data | `DateTime` |
| null możliwy | `DateTime?` |
| obiekt `{}` | osobna klasa |
| tablica `[]` | `List<>` |

Wklej plik z repozytorium — zmień nazwy klas i pól według JSON z zadania.

---

## Krok 9 — IDbService.cs

Wklej z repozytorium — zmień tylko nazwy metod i typy DTO według zadania.

---

## Krok 10 — DbService.cs

Wklej z repozytorium ze zmianami:

**GET:**
- Zmień nazwę DbSet (`_context.Orders` → nazwa z zadania)
- Zmień pola w `Select` według DTO
- Nawigacyjne właściwości EF Core robi sam — po prostu `e.Status.Name`

**PUT:**
- Struktura transakcji — kopiuj bez zmian
- Zmień sprawdzenia według wymagań zadania (404, 409)
- ⚠️ Sprawdź jak nazywa się PK w wygenerowanym modelu — może być `StatusId` albo `StatusStatusId` zależnie od bazy

---

## Krok 11 — Controller

Wklej z repozytorium ze zmianami:
- Zmień nazwę kontrolera
- Zmień routa jeśli zadanie wymaga innego
- HTTP kody — zawsze takie same:
  - 200 OK — GET i PUT sukces
  - 404 Not Found — brak zamówienia lub statusu
  - 409 Conflict — zamówienie już zrealizowane
  - 400 Bad Request — błędne dane

---

## Krok 12 — Program.cs

Wklej z repozytorium — zmień tylko namespace.

---

## Krok 13 — Sprawdzenie przez Swagger

Uruchom projekt → otwórz `http://localhost:XXXX/swagger`

**GET** — sprawdź:
- id który istnieje → 200
- id który nie istnieje → 404

**PUT** — sprawdź:
- poprawne dane → 200
- zamówienie już zrealizowane → 409
- status nie istnieje → 404
- zamówienie nie istnieje → 404

---

## Ważne rzeczy do zapamiętania

- ⚠️ Po scaffold zawsze usuń `OnConfiguring` z `DatabaseContext.cs`
- ⚠️ Sprawdź nazwę PK w wygenerowanych modelach — może się różnić
- ⚠️ `dotnet-ef` musi być zainstalowany globalnie — sprawdź przed kolokwium
- ⚠️ VPN musi być włączony przed scaffold i przed uruchomieniem projektu
- ⚠️ `.gitignore` z `bin/` i `obj/` — obowiązkowe, bez tego -50%
