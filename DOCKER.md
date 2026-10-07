# Docker deployment

This Compose stack runs the Next.js app, Spring Boot API/WebSocket server,
MariaDB, Redis, and phpMyAdmin. The MariaDB image imports
`nextjs/database/taxicap_actual.sql` the first time its data volume is created.
The existing tile, Nominatim, and Valhalla containers stay outside this Compose
project and are reached through their published host ports.

## First start

1. Copy `.env.example` to `.env`.
2. Set unique database passwords and copy the existing JWT, data-encryption,
   VAPID, and map API key values from `nextjs/.env` into `.env`. The JWT signing
   key must match the one used for existing tokens.
3. Make sure the external map containers publish ports 8001, 8003, and 8002 on
   the host. The defaults in `.env.example` use `host.docker.internal`.
4. From the project root, run:

   ```powershell
   docker compose up --build -d
   ```

Open the app at <http://localhost:3000>, phpMyAdmin at
<http://localhost:8081>, and the Spring API at <http://localhost:8080>.
MariaDB is published on port 3307 by default so it does not collide with an
XAMPP instance on port 3306. Set `MARIADB_PORT=3306` after stopping XAMPP if
you need MariaDB on the default host port.

## Database and uploads

The SQL dump is imported only when the `mariadb_data` volume is first
initialized. To load a newer dump into an existing deployment, back up the
database and import the dump explicitly; restarting the stack does not rerun
initialization scripts. User uploads are persisted in the `user_uploads`
volume.

The SQL file contains application data. Keep backups and the `.env` file
private, and do not publish either file or its contents.
