# Progress Update

## Project
Hospital Management System (Spring Boot + Thymeleaf + MySQL)

## Completed Updates

### 1. Doctor account auto-creation on Admin add-doctor flow
**Files updated**
- `src/main/java/com/hospital/management/service/DoctorService.java`
- `src/main/java/com/hospital/management/controller/AdminController.java`

**What changed**
- When Admin adds a doctor, the system now creates a linked `User` account automatically.
- New doctor account settings:
  - Role: `DOCTOR`
  - Initial password: `123` (stored encoded with `PasswordEncoder`)
  - Enabled: `true`
  - First login flag: `true`
- Username is generated from doctor full name, with numeric suffix if needed to keep uniqueness.
- Admin success message now shows doctor login username (password is not shown in flash message).

---

### 2. Railway-ready database + port configuration
**File updated**
- `src/main/resources/application.properties`

**What changed**
- Datasource now supports Railway environment variables with local fallback:
  - `spring.datasource.url=${SPRING_DATASOURCE_URL:jdbc:mysql://${MYSQLHOST:localhost}:${MYSQLPORT:3306}/${MYSQLDATABASE:hospital_mngt}?createDatabaseIfNotExist=true&useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC}`
  - `spring.datasource.username=${SPRING_DATASOURCE_USERNAME:${MYSQLUSER:root}}`
  - `spring.datasource.password=${SPRING_DATASOURCE_PASSWORD:${MYSQLPASSWORD:root}}`
- Server port is now Railway-compatible:
  - `server.port=${PORT:8080}`

---

### 3. Automatic default ward seeding for fresh Railway DB
**File updated**
- `src/main/java/com/hospital/management/DataInitializer.java`

**What changed**
- Added startup seeding to ensure required wards exist (idempotent behavior).
- Wards seeded:
  - `Male General Ward` (`MALE`, capacity `20`)
  - `Female General Ward` (`FEMALE`, capacity `20`)
  - `Mixed ICU` (`MIXED`, capacity `10`)
- Duplicate inserts are safely ignored when another instance inserts first.

---

### 4. Admin bootstrap config via environment
**File updated**
- `src/main/java/com/hospital/management/DataInitializer.java`

**What changed**
- Admin bootstrap username/password are now configurable:
  - `APP_BOOTSTRAP_ADMIN_USERNAME` (default: `admin`)
  - `APP_BOOTSTRAP_ADMIN_PASSWORD` (default: `admin123`)
- Initial admin creation now uses these values.

## Railway Deployment Notes

Set these environment variables in Railway app service:
- `MYSQLHOST`
- `MYSQLPORT`
- `MYSQLDATABASE`
- `MYSQLUSER`
- `MYSQLPASSWORD`
- `APP_BOOTSTRAP_ADMIN_PASSWORD` (recommended to override default)

After first startup on Railway:
- tables are auto-created (`spring.jpa.hibernate.ddl-auto=update`)
- wards are auto-seeded
- male/female ward options become available during patient admission

