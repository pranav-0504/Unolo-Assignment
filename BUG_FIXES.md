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
