# Database Schema Documentation

> **Note to AI Assistant:** Keep this file updated with all schema changes. Update after adding tables, modifying fields, or changing relationships.

---

## Table of Contents

1. [Database Overview](#database-overview)
2. [Schema Diagram](#schema-diagram)
3. [Tables/Collections](#tablescollections)
4. [Relationships](#relationships)
5. [Indexes](#indexes)
6. [Security Rules](#security-rules)
7. [Migrations](#migrations)
8. [Data Validation](#data-validation)
9. [Query Examples](#query-examples)

---

## Database Overview

### Database Type

- **Type:** [PostgreSQL / MongoDB / Firebase Firestore / MySQL / etc.]
- **Version:** [Version number]
- **Hosting:** [Where it's hosted]
- **Backup Strategy:** [Backup frequency and retention]

### Design Principles

- **Normalization:** [3NF / Denormalized for performance / etc.]
- **Naming Conventions:** [snake_case / camelCase / PascalCase]
- **Soft Deletes:** [Yes/No] - Use `deleted_at` field
- **Timestamps:** All tables have `created_at` and `updated_at`
- **UUIDs vs Integers:** [Strategy for primary keys]

---

## Schema Diagram

### Entity Relationship Diagram

```
[Create ASCII or link to diagram tool]

Example for SQL database:

┌─────────────────┐         ┌─────────────────┐
│     users       │         │     todos       │
├─────────────────┤         ├─────────────────┤
│ id (PK)         │<──────┐ │ id (PK)         │
│ email           │       └─│ user_id (FK)    │
│ name            │         │ text            │
│ created_at      │         │ completed       │
│ updated_at      │         │ created_at      │
└─────────────────┘         └─────────────────┘
```

### Overview

[Brief description of the overall data model]

---

## Tables/Collections

> **For AI Assistants:** Update this section when adding/modifying tables or fields.

### Table: users

**Purpose:** Stores user account information and authentication data.

**Fields:**

| Field | Type | Constraints | Default | Description |
|-------|------|-------------|---------|-------------|
| `id` | INTEGER/UUID | PRIMARY KEY, AUTO_INCREMENT | - | Unique user identifier |
| `email` | VARCHAR(255) | NOT NULL, UNIQUE | - | User email address |
| `name` | VARCHAR(255) | NOT NULL | - | User display name |
| `password_hash` | VARCHAR(255) | NOT NULL | - | Bcrypt hashed password |
| `email_verified` | BOOLEAN | NOT NULL | false | Whether email is verified |
| `role` | ENUM | NOT NULL | 'user' | User role: 'user', 'admin' |
| `created_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Account creation time |
| `updated_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Last update time |
| `deleted_at` | TIMESTAMP | NULL | NULL | Soft delete timestamp |

**Indexes:**
- PRIMARY KEY on `id`
- UNIQUE INDEX on `email`
- INDEX on `created_at` (for sorting)

**Example:**
```json
{
  "id": 1,
  "email": "user@example.com",
  "name": "John Doe",
  "password_hash": "$2b$10$...",
  "email_verified": true,
  "role": "user",
  "created_at": "2026-01-15T10:00:00Z",
  "updated_at": "2026-01-15T10:00:00Z",
  "deleted_at": null
}
```

---

### Table: [table_name_2]

**Purpose:** [Brief description of what this table stores]

**Fields:**

| Field | Type | Constraints | Default | Description |
|-------|------|-------------|---------|-------------|
| `id` | [type] | PRIMARY KEY | - | [description] |
| `[field1]` | [type] | [constraints] | [default] | [description] |
| `[field2]` | [type] | [constraints] | [default] | [description] |
| `created_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Creation time |
| `updated_at` | TIMESTAMP | NOT NULL | CURRENT_TIMESTAMP | Last update time |

**Indexes:**
- [List indexes]

**Example:**
```json
{
  "id": 1,
  "field1": "value",
  "field2": "value",
  "created_at": "2026-01-15T10:00:00Z",
  "updated_at": "2026-01-15T10:00:00Z"
}
```

---

### For Firebase Firestore

If using Firestore, document collections:

### Collection: users

**Path:** `/users/{userId}`

**Purpose:** Stores user profile data

**Fields:**

| Field | Type | Optional | Description |
|-------|------|----------|-------------|
| `uid` | string | No | Firebase Auth user ID |
| `email` | string | No | User email address |
| `displayName` | string | Yes | User display name |
| `photoURL` | string | Yes | Profile picture URL |
| `createdAt` | timestamp | No | Account creation time |
| `updatedAt` | timestamp | No | Last update time |
| `settings` | object | Yes | User preferences |

**Security Rules:**
```javascript
match /users/{userId} {
  allow read, write: if request.auth != null && request.auth.uid == userId;
}
```

**Example Document:**
```json
{
  "uid": "abc123",
  "email": "user@example.com",
  "displayName": "John Doe",
  "photoURL": "https://...",
  "createdAt": Timestamp,
  "updatedAt": Timestamp,
  "settings": {
    "theme": "dark",
    "notifications": true
  }
}
```

**Subcollections:**
- `/users/{userId}/todos` - User's todo items

---

## Relationships

### One-to-Many Relationships

#### users → todos
- One user has many todos
- Foreign key: `todos.user_id` references `users.id`
- Delete cascade: When user deleted, all todos are deleted

#### [parent] → [child]
- [Description of relationship]
- Foreign key: [field reference]
- Delete behavior: [CASCADE / SET NULL / RESTRICT]

### Many-to-Many Relationships

#### [table1] ↔ [table2]
- Junction table: `table1_table2`
- [Description of relationship]

**Junction Table: table1_table2**

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| `id` | INTEGER | PRIMARY KEY | Unique identifier |
| `table1_id` | INTEGER | FOREIGN KEY | References table1.id |
| `table2_id` | INTEGER | FOREIGN KEY | References table2.id |
| `created_at` | TIMESTAMP | NOT NULL | Creation time |

---

## Indexes

> **Purpose:** Indexes improve query performance. Update this section when adding indexes.

### Current Indexes

| Table | Index Name | Columns | Type | Purpose |
|-------|------------|---------|------|---------|
| users | idx_users_email | email | UNIQUE | Fast email lookup for login |
| users | idx_users_created | created_at | BTREE | Sorting users by join date |
| todos | idx_todos_user | user_id | BTREE | Fast user todos lookup |
| todos | idx_todos_user_date | user_id, date | COMPOSITE | User todos by date |

### Index Performance

- **Total indexes:** [Number]
- **Average query time:** [Time]
- **Slow queries:** [Link to monitoring]

---

## Security Rules

### For Firebase Firestore

**File:** `firestore.rules`

```javascript
rules_version = '2';

service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isAuthenticated() {
      return request.auth != null;
    }
    
    function isOwner(userId) {
      return request.auth.uid == userId;
    }
    
    // Users collection
    match /users/{userId} {
      allow read: if isAuthenticated();
      allow create: if isAuthenticated() && isOwner(userId);
      allow update, delete: if isAuthenticated() && isOwner(userId);
      
      // User's todos subcollection
      match /todos/{todoId} {
        allow read, write: if isAuthenticated() && isOwner(userId);
      }
    }
    
    // Shared collections
    match /shared_items/{itemId} {
      allow read: if isAuthenticated() && 
                     resource.data.sharedWith[request.auth.uid] == true;
      allow write: if isAuthenticated() && 
                      resource.data.owner == request.auth.uid;
    }
  }
}
```

### For SQL Database

**Row-Level Security (PostgreSQL):**

```sql
-- Enable RLS on users table
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their own data
CREATE POLICY user_isolation ON users
  USING (id = current_user_id());

-- Policy: Users can only modify their own data
CREATE POLICY user_modification ON users
  FOR UPDATE
  USING (id = current_user_id());
```

---

## Migrations

### Migration Files Location

- **Directory:** `migrations/` or `src/migrations/`
- **Naming:** `YYYYMMDDHHMMSS_migration_name.sql` or `.ts`

### Creating Migrations

```bash
# Create new migration
npm run db:migrate:create [migration-name]

# Example:
npm run db:migrate:create add_user_roles
```

### Running Migrations

```bash
# Run all pending migrations
npm run db:migrate

# Rollback last migration
npm run db:migrate:rollback

# Reset database (caution!)
npm run db:reset
```

### Migration History

| Date | Version | Migration | Description | Author |
|------|---------|-----------|-------------|--------|
| 2026-01-15 | 001 | `create_users` | Initial users table | [Name] |
| 2026-01-16 | 002 | `create_todos` | Add todos table | [Name] |
| [Date] | [Ver] | [Name] | [Description] | [Author] |

### Example Migration (SQL)

**Up Migration:**
```sql
-- migrations/001_create_users.up.sql
CREATE TABLE users (
  id SERIAL PRIMARY KEY,
  email VARCHAR(255) NOT NULL UNIQUE,
  name VARCHAR(255) NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_users_email ON users(email);
```

**Down Migration:**
```sql
-- migrations/001_create_users.down.sql
DROP TABLE IF EXISTS users;
```

---

## Data Validation

### Validation Rules

#### users table

- **email:**
  - Format: Valid email format (RFC 5322)
  - Length: 5-255 characters
  - Uniqueness: Must be unique
  - Example: `user@example.com`

- **password:**
  - Min length: 8 characters
  - Requirements: At least 1 uppercase, 1 lowercase, 1 number
  - Stored as: bcrypt hash with salt rounds = 10

- **name:**
  - Length: 1-255 characters
  - Format: Letters, spaces, hyphens, apostrophes only
  - Example: `John O'Brien-Smith`

#### [other_table]

- **[field]:**
  - [Validation rules]

### Data Constraints

```sql
-- Email format check
ALTER TABLE users ADD CONSTRAINT email_format 
  CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');

-- Date range check
ALTER TABLE events ADD CONSTRAINT valid_date_range
  CHECK (end_date >= start_date);

-- Enum values
ALTER TABLE todos ADD CONSTRAINT valid_status
  CHECK (status IN ('pending', 'in_progress', 'completed'));
```

---

## Query Examples

### Common Queries

#### Get user by email

**SQL:**
```sql
SELECT * FROM users 
WHERE email = $1 
AND deleted_at IS NULL;
```

**Firestore:**
```typescript
const userRef = db.collection('users')
  .where('email', '==', email)
  .limit(1);
const snapshot = await userRef.get();
```

#### Get user's todos

**SQL:**
```sql
SELECT * FROM todos 
WHERE user_id = $1 
AND deleted_at IS NULL
ORDER BY created_at DESC;
```

**Firestore:**
```typescript
const todosRef = db.collection('users').doc(userId)
  .collection('todos')
  .orderBy('createdAt', 'desc');
const snapshot = await todosRef.get();
```

#### Complex query with JOIN

**SQL:**
```sql
SELECT u.name, COUNT(t.id) as todo_count
FROM users u
LEFT JOIN todos t ON u.id = t.user_id
WHERE u.created_at > NOW() - INTERVAL '30 days'
GROUP BY u.id, u.name
HAVING COUNT(t.id) > 5
ORDER BY todo_count DESC;
```

#### Pagination

**SQL:**
```sql
SELECT * FROM todos
WHERE user_id = $1
ORDER BY created_at DESC
LIMIT $2 OFFSET $3;
```

**Firestore:**
```typescript
const todosRef = db.collection('users').doc(userId)
  .collection('todos')
  .orderBy('createdAt', 'desc')
  .limit(pageSize)
  .startAfter(lastDoc);
```

### Performance Considerations

- Always use indexes for `WHERE` clause columns
- Avoid `SELECT *` in production - specify needed columns
- Use `LIMIT` for large result sets
- Consider pagination for user-facing queries
- Use `EXPLAIN` (SQL) to analyze query performance

---

## Backup & Recovery

### Backup Strategy

- **Frequency:** [Daily / Hourly / Continuous]
- **Retention:** [30 days / 90 days]
- **Location:** [S3 / Google Cloud Storage / etc.]
- **Encryption:** [Yes/No]

### Backup Commands

**PostgreSQL:**
```bash
# Backup
pg_dump -U username -d database_name -F c -f backup.dump

# Restore
pg_restore -U username -d database_name backup.dump
```

**MongoDB:**
```bash
# Backup
mongodump --uri="mongodb://..." --out=/backup/

# Restore
mongorestore --uri="mongodb://..." /backup/
```

**Firestore:**
```bash
# Backup (via gcloud)
gcloud firestore export gs://bucket-name

# Restore
gcloud firestore import gs://bucket-name/[export-folder]
```

### Testing Backups

- **Last tested:** [Date]
- **Test frequency:** [Monthly / Quarterly]
- **Test procedure:** Restore to staging and verify data integrity

---

## Data Privacy & Compliance

### Personal Data

**Fields containing PII:**
- `users.email`
- `users.name`
- [other fields]

**Retention policy:**
- Active users: Data retained indefinitely
- Deleted accounts: Hard delete after [X days]
- Backups: Excluded from long-term retention

### GDPR Compliance

- ✅ Right to erasure: Implemented via `DELETE /api/user/account`
- ✅ Right to data portability: Export API at `/api/user/export`
- ✅ Data encryption: At rest and in transit
- ✅ Audit logging: User data access logged

---

## Schema Change Workflow

### Before Making Changes

1. **Document the change** in this file first
2. **Create migration** with up and down scripts
3. **Test on local** database
4. **Test on staging** environment
5. **Review with team** (for major changes)
6. **Update related code** (API, models, tests)

### After Making Changes

1. **Update this documentation**
2. **Update tests** to reflect schema changes
3. **Update API documentation** if endpoints affected
4. **Run migration** on staging
5. **Verify** all functionality works
6. **Deploy to production** during low-traffic period
7. **Monitor** for errors after deployment

---

## Monitoring & Alerts

### Query Performance

- **Slow query threshold:** > [X] ms
- **Monitoring tool:** [Tool name]
- **Alert on:** Queries exceeding threshold

### Database Health

- **Connection pool:** [Current/Max connections]
- **Disk usage:** [Current/Max storage]
- **CPU usage:** [Average]
- **Memory usage:** [Average]

### Alerts Configured

- 🚨 Connection pool > 80% capacity
- 🚨 Disk usage > 85%
- 🚨 Slow query > [X] ms
- 🚨 Failed queries > [Y] per minute

---

## FAQ

### How do I add a new field?

1. Create migration: `npm run db:migrate:create add_field_name`
2. Write up and down migrations
3. Update this documentation
4. Update TypeScript types
5. Test locally, then staging, then production

### How do I change a field type?

1. Consider backward compatibility
2. Create migration with ALTER TABLE statement
3. Update application code to handle new type
4. Test thoroughly on staging
5. Deploy during low-traffic period

### How do I delete a table?

1. Ensure no code references it
2. Create migration with DROP TABLE
3. Archive any important data
4. Test on staging
5. Deploy with careful monitoring

---

## Resources

### Internal
- [AGENTS.md](../AGENTS.md) - Schema update guidelines
- [DEPLOYMENT.md](DEPLOYMENT.md) - Migration deployment procedures
- [TESTS.md](TESTS.md) - Database testing strategies

### External
- [Database Documentation] - Official docs for your database
- [Migration Tool Docs] - Migration library documentation
- [ORM Documentation] - If using an ORM

---

**Last Updated:** [Date]
**Database Version:** [Version]
**Total Tables/Collections:** [Number]
**Total Indexes:** [Number]
