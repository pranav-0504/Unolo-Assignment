### Bug 1: Login intermittently fails even with correct credentials
- File: backend/routes/auth.js
- Issue: bcrypt.compare() was used without await
- Fix: Added await to bcrypt.compare()
- Why: bcrypt.compare() returns a Promise

### Bug 2: Sensitive data included in JWT payload
- File: backend/routes/auth.js
- Issue: Password hash was included in JWT payload
- Fix: Removed password from JWT payload
- Why: JWT payload can be decoded on the client

### Bug 3: JWT verification failed intermittently
- File: backend/middleware/auth.js
- Issue: JWT secret fallback caused mismatch
- Fix: Centralized JWT_SECRET and removed default fallback
- Why: Token must be signed and verified with the same secret


### Bug 4: JWT secret used inconsistently across files
- File: backend/routes/auth.js
- Issue: JWT_SECRET imported but process.env.JWT_SECRET used directly
- Fix: Used centralized JWT_SECRET everywhere
- Why: Prevents secret mismatch and improves maintainability

### Bug 5: Location data not saved correctly during check-in
- File: backend/routes/checkin.js
- Issue: Used non-existent columns `lat` and `lng`
- Fix: Replaced with correct column names `latitude` and `longitude`
- Why: SQLite schema defines columns as latitude/longitude


### Bug 6: Incorrect HTTP status code for validation error
- File: backend/routes/checkin.js
- Issue: API returned 200 status for missing required fields
- Fix: Changed response status to 400 Bad Request
- Why: Client errors should not return success status


### Bug 7: SQL injection risk in history filters
- File: backend/routes/checkin.js
- Issue: User input directly interpolated into SQL query
- Fix: Used parameterized queries with placeholders
- Why: Prevents SQL injection and improves query safety
















