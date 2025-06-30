# nextjs-clean-architecture
Searching for best nextjs app architecture

Main folders structure

## FE layer (View)

📂 /app
- all FE (nextjs framework) code
- actions
- _components?

📂 /components
- stays due to the legacy, or move to `/app` folder?


## DAL - database access layer (ORM)

📂 /drizzle
- ORM generated DB related code ()

📂 /db
- ORM db connection

📂 /db/schema
- ORM table schemas (types exported, but copied to other layer too - entities?)

## Controllers (Interface Adapters)

📂 /interface-adapters
📂 /interface-adapters/controllers or:
📂 /controllers
- interface layer, connection point between FE and Business logic

## Use-cases (Application Layer)

📂 /application
📂 /application/use-cases
📂 /application/use-cases/{some_model}
📂 /application/use-cases/content
- individual use-cases (one per file)

📂 /entities
- models (ts, zod, TS schemas)
- errors

## DAL (DB Access Layer)

_Warstwa bezpośredniej komunikacji z bazą lub innym zasobem zewnętrznym (np. chmurą)_.

📂 /infrastructure

📂 /infrastructure/repositories

- Zawiera klasy modeli grupujące metody CRUD, wykorzystujące obiekt `db` drizzle do tworzenia bezpośrednich zapytań do bazy, rzuca obiekty błędów powiązane z bazą

- zawiera klasy obiektów MOCK do testowania aplikacji w izolacji

📂 /infrastructure/services

- zawiera serwisy powiązane z zewnętrznymi vendorami (zewnętrzna autoryzacja, serwisy powiązane z DB itp.)



📂 /tests
- all tests, unit, e2e, etc.


## Data flow (form example)

### CreateRUD

```tsx
// Frameworks & Drivers - FE layer (nextjs)
// 📂 /app/*.* 
// layout.tsx => page.tsx [form] => action.ts
'use server'
const data = formData
createDataController(data, session)
return {success}
throw CustomViewError()
return {error}

// Interface Adapters Layer (controllers)
// walidacja danych
// autentykacja usera
// 📂 /controllers/some/some.controller.ts
!session => throw CustomUnauthenticatedError()
zod.schema.validate(data)
const some = createSomeUseCase(data, id)
// reshape data to FE format (js object) - UI friendly
// security reason
return presenter(some)


// Application Layer (use-cases)
// autoryzacja uzera
// 📂 /use-cases/some/create-some.use-case.ts
if (userHasPower) do { sth } or throw CustomUseCaseError()
const newSome = someRepository.createSome({...})
return newSome


// Infrastructure - Data Layer (DAL)
// 📂 /infrastructure/repositories/some.repository.ts
class SomeRepository implements ISomeRepository
createSome() {
    const query = await db.insert(some).values(data).returning()
    const created = await query.execute()
    if (!created) => throw CustomDBLayerError('')
    return created;    
}

```

### CReadUD

```tsx
// Frameworks & Drivers - FE layer (nextjs)
// 📂 /app/*.* 
// layout.tsx => page.tsx => some list element

return readSomeDataController(data, session) or
throw UnauthenticatedError() or AuthenticationError()

// Interface Adapters Layer (controllers)
// walidacja danych
// autentykacja usera
// 📂 /controllers/some/getSome.controller.ts
!session => throw CustomUnauthenticatedError()

const some = getSomeUseCase(id)
// reshape data to FE format (js object) - UI friendly
// security reason
return presenter(some)


// Application Layer (use-cases)
// autoryzacja uzera
// 📂 /use-cases/some/getSome.use-case.ts
if (userHasPower) do { sth } or throw CustomUseCaseError()
const newSome = someRepository.getSome({...})
return newSome


// Infrastructure - Data Layer (DAL)
// 📂 /infrastructure/repositories/some.repository.ts
class SomeRepository implements ISomeRepository
getSome() {
    const query = await db.insert(some).values(data).returning()
    const created = await query.execute()
    if (!created) => throw CustomDBLayerError('')
    return created;    
}

```