# WHAT THE PROJECT IS

full-stack-fastapi-template is an official, full-stack monorepo template
built by FastAPI's own creator. It includes a FastAPI + SQLModel +
PostgreSQL backend, a separate frontend app, and Docker-based local dev
setup with Alembic migrations. I focused specifically on the backend,
since it directly builds on what I already know from my own
expense-tracker project.

## Models and Schemas: Unified via SQLModel

My own project keeps models.py (database structure) and schemas.py
(request/response validation) completely separate and independent - a
deliberate choice made during the Day 16 budgets feature, to avoid
inheritance coupling two things that might need to change independently.

This template takes the opposite approach, using SQLModel to unify both
into one inheritance chain. I verified this directly in models.py:

- `ItemBase(SQLModel)` defines the shared fields (title, description)
  once
- `ItemCreate(ItemBase)` inherits them for request validation
- `Item(ItemBase, table=True)` inherits them too, adds database-specific
  fields (id, owner_id, timestamps), and `table=True` is what actually
  makes this class map to a real database table
- `ItemPublic(ItemBase)` inherits them again for the response shape

This avoids repeating shared fields across multiple classes. I don't
think this makes my own project's independent-schema choice wrong -
SQLModel's `table=True` mechanism is specifically designed to make this
particular inheritance pattern safe and predictable in a way plain
Python inheritance isn't. Inheritance suits a stable, well-scoped
project like this template; independence suits a project that's still
actively changing, like mine.

## Dependency Aliases (SessionDep)

Instead of writing `db: Session = Depends(get_db)` in every single route
signature like I do, this project defines it once in deps.py:

```python
SessionDep = Annotated[Session, Depends(get_db)]
```

Every route then just uses `session: SessionDep`. This is conceptually
the same idea as the resolve_task helper I built on Day 10 - eliminate
duplicated boilerplate by defining it once and reusing it everywhere,
just applied to type annotations instead of logic. I've documented this
as a genuine improvement worth adopting in my own expense-tracker's
CLAUDE.md.

## Routes and CRUD - A Real Inconsistency

The project has a separate crud.py file with plain functions (like
create_item) meant to hold data-access logic separately from routes,
keeping routes focused on HTTP concerns. I verified crud.py genuinely
contains a create_item function with real database logic
(session.add/commit/refresh - the exact same mechanism I already use in
my own project, just called `session` instead of `db`).

But checking the actual route in items.py, create_item does NOT call
crud.create_item() at all - it duplicates the same three lines directly
inline instead. I confirmed this by searching every route file for any
call to crud.create_item and found zero results. This means crud.py's
create_item function is effectively unused, at least for this specific
route - a real, honest inconsistency in an official, expert-maintained
template, not something invented for a tutorial.

## A Genuine, Unplanned Discovery

While reviewing deps.py, Claude Code flagged `except InvalidTokenError,
ValidationError:` as an old Python 2 syntax error. I checked it with
py_compile - no error. I then tested the exact pattern directly and
confirmed it genuinely catches both exception types correctly. Research
revealed the real explanation: Python 3.14 (PEP 758) made this exact
unparenthesized syntax valid, equivalent to using parentheses. Claude
Code's claim reflected outdated Python knowledge (correct for any
version before 3.14), not a hallucination - a real, live example of
verification catching a claim that was accurate historically but wrong
for the current context.