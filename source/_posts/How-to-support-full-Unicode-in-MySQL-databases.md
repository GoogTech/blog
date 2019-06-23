---
title: How to support full Unicode in MySQL databases
date: 2019-06-23 08:28:41
tags: [MySQL]
---

## How to support full Unicode in MySQL databases · Mathias Bynens

😂*Alternative title: The things we do to store U+1F4A9 PILE OF POO (💩) correctly.*

*Are you using MySQL’s utf8 charset in your databases? In this write-up I’ll explain why you should switch to utf8mb4 instead, and how to do it.*

## UTF-8
*The UTF-8 encoding can represent every symbol in the Unicode character set, which ranges from U+000000 to U+10FFFF. That’s 1,114,112 possible symbols. (Not all of these Unicode code points have been assigned characters yet, but that doesn’t stop UTF-8 from being able to encode them.)*

*UTF-8 is a variable-width encoding; it encodes each symbol using one to four 8-bit bytes. Symbols with lower numerical code point values are encoded using fewer bytes. This way, UTF-8 is optimized for the common case where ASCII characters and other BMP symbols (whose code points range from U+000000 to U+00FFFF) are used — while still allowing astral symbols (whose code points range from U+010000 to U+10FFFF) to be stored.*


## MySQL’s utf8
*For a long time, I was using MySQL’s utf8 charset for databases, tables, and columns, assuming it mapped to the UTF-8 encoding described above. By using utf8, I’d be able to store any symbol I want in my database — or so I thought.*

*While writing about JavaScript’s internal character encoding, I noticed that there was no way to insert the U+1D306 TETRAGRAM FOR CENTRE (𝌆) symbol into the MySQL database behind this site. The column I was trying to update had the utf8_unicode_ci collation, and the connection charset was set to utf8.*

```t
# just to emphasize that the connection charset is set to `utf8`
mysql> SET NAMES utf8; 
Query OK, 0 rows affected (0.00 sec)

mysql> UPDATE database_name.table_name SET column_name = 'foo𝌆bar' WHERE id = 9001;
Query OK, 1 row affected, 1 warning (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 1

mysql> SELECT column_name FROM database_name.table_name WHERE id = 9001;
+-------------+
| column_name |
+-------------+
| foo         |
+-------------+
1 row in set (0.00 sec)
```
*The content got truncated at the first astral Unicode symbol, in this case 𝌆 — so, attempting to insert foo𝌆bar actually inserted foo instead, resulting in data loss (and possibly introducing security issues; see below). MySQL returned a warning message, too:*

```mysql
mysql> SHOW WARNINGS;
+---------+------+------------------------------------------------------------------------------+
| Level   | Code | Message                                                                      |
+---------+------+------------------------------------------------------------------------------+
| Warning | 1366 | Incorrect string value: '\xF0\x9D\x8C\x86' for column 'column_name' at row 1 |
+---------+------+------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
*Turns out MySQL’s utf8 charset only partially implements proper UTF-8 encoding. It can only store UTF-8-encoded symbols that consist of one to three bytes; encoded symbols that take up four bytes aren’t supported.*

*Since astral symbols (whose code points range from U+010000 to U+10FFFF) each consist of four bytes in UTF-8, you cannot store them using MySQL’s utf8 implementation.*

*This doesn’t just affect the 𝌆 character, but more important symbols like U+01F4A9 PILE OF POO (💩) as well. In total, that’s 1,048,575 possible code points you can’t use. In fact, MySQL’s utf8 only allows you to store 5.88% ((0x00FFFF + 1) / (0x10FFFF + 1)) of all possible Unicode code points. Proper UTF-8 can encode 100% of all Unicode code points.*

*As shown above, `this behavior can lead to data loss`, but it gets worse — it can result in security vulnerabilities.MySQL’s utf8 encoding is awkwardly named, as it’s different from proper UTF-8 encoding. It doesn’t offer full Unicode support, which can lead to data loss or security vulnerabilities.*

## MySQL’s utf8mb4
*Luckily, `MySQL 5.5.3 (released in early 2010)` introduced a new encoding called utf8mb4 which maps to proper UTF-8 and thus fully supports Unicode, including astral symbols.*

*Switching from MySQL’s utf8 to utf8mb4*
### Step 1: Create a backup
*Create a backup of all the databases on the server you want to upgrade. Safety first!*

### Step 2: Upgrade the MySQL server
*Upgrade the MySQL server to v5.5.3+, or ask your server administrator to do it for you.*

### Step 3: Modify databases, tables, and columns
*Change the character set and collation properties of the databases, tables, and columns to use utf8mb4 instead of utf8.*

- For each database :
*`ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci`;*
- For each table :
*`ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci`;*
- For each column :
*`ALTER TABLE table_name CHANGE column_name column_name VARCHAR(191) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci`;*
- (Don’t blindly copy-paste this! The exact statement depends on the column type, maximum length, and other properties. The above line is just an example for a `VARCHAR` column.)
Since utf8mb4 is fully backwards compatible with utf8, no mojibake or other forms of data loss should occur. (But you have a backup, right?)

### Step 4: Check the maximum length of columns and index keys
*This is probably the most tedious part of the whole upgrading process.*

*When converting from utf8 to utf8mb4, the maximum length of a column or index key is unchanged in terms of bytes. Therefore, it is smaller in terms of characters, because the maximum length of a character is now four bytes instead of three.*

*For example, a TINYTEXT column can hold up to 255 bytes, which correlates to 85 three-byte or 63 four-byte characters. Let’s say you have a TINYTEXT column that uses utf8 but must be able to contain more than 63 characters. Given this requirement, you can’t convert this column to utf8mb4 unless you also change the data type to a longer type such as TEXT — because if you’d try to fill it with four-byte characters, you’d only be able to enter 63 characters, but not more.*

*The same goes for index keys. The InnoDB storage engine has a maximum index length of 767 bytes, so for utf8 or utf8mb4 columns, you can index a maximum of 255 or 191 characters, respectively. If you currently have utf8 columns with indexes longer than 191 characters,you will need to index a smaller number of characters when using utf8mb4. (Because of this, I had to change some indexed VARCHAR(255) columns to VARCHAR(191).)*

*Section 10.1.11 of the `MySQL 5.5` Reference Manual has some more information on this.*

### Step 5: Modify connection, client, and server character sets
*In your application code, set the connection character set to utf8mb4. This can be done by simply replacing any variants of SET NAMES utf8 with SET NAMES utf8mb4. If your old SET NAMES statement specified the collation, make sure to change that as well, e.g. `SET NAMES utf8 COLLATE utf8_unicode_ci becomes SET NAMES utf8mb4 COLLATE utf8mb4_unicode_ci.`*

*Make sure to set the client and server character set as well. I have the following in my MySQL configuration file (`/etc/my.cnf`):*
```mysql
[client]
default-character-set = utf8mb4

[mysql]
default-character-set = utf8mb4

[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
```

*You can easily confirm these settings work correctly:*
```t
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character\_set\_%' OR Variable_name LIKE 'collation%';
+--------------------------+--------------------+
| Variable_name            | Value              |
+--------------------------+--------------------+
| character_set_client     | utf8mb4            |
| character_set_connection | utf8mb4            |
| character_set_database   | utf8mb4            |
| character_set_filesystem | binary             |
| character_set_results    | utf8mb4            |
| character_set_server     | utf8mb4            |
| character_set_system     | utf8               |
| collation_connection     | utf8mb4_unicode_ci |
| collation_database       | utf8mb4_unicode_ci |
| collation_server         | utf8mb4_unicode_ci |
+--------------------------+--------------------+
10 rows in set (0.00 sec)
```
As you can see, all the relevant options are set to utf8mb4, except for character_set_filesystem which should be binary unless you’re on a file system that supports multi-byte UTF-8-encoded characters in file names, and character_set_system which is always utf8 and can’t be overridden.

- *`Note: The default character set and collation can be configured at some other levels as well.`*

### Step 6: Repair and optimize all tables
*After upgrading the MySQL server and making the necessary changes explained above, make sure to repair and optimize all databases and tables. I didn’t do this right away after upgrading (I didn’t think it was necessary, as everything seemed to work fine at first glance), and ran into some weird bugs where UPDATE statements didn’t have any effect, even though no errors were thrown.*

*You could run the following MySQL queries for each table you want to repair and optimize:*
```t
# For each table
REPAIR TABLE table_name;
OPTIMIZE TABLE table_name;
```

*Luckily, this can easily be done in one go using the command-line mysqlcheck utility:*
```mysql
$ mysqlcheck -u root -p --auto-repair --optimize --all-databases
```
*This will prompt for the root user’s password, after which all tables in all databases will be repaired and optimized.*

## Summary
*`Never use utf8 in MySQL — always use utf8mb4 instead.` Updating your databases and code might take some time, but it’s definitely worth the effort. Why would you arbitrarily limit the set of symbols that can be used in your database? Why would you lose data every time a user enters an astral symbol as part of a comment or message or whatever it is you store in your database? There’s no reason not to strive for full Unicode support everywhere. Do the right thing, and use utf8mb4. 🍻 ヾ(￣▽￣)Bye~Bye~*



- *🙂expressing thanks : https://mathiasbynens.be/notes/mysql-utf8mb4#utf8-to-utf8mb4*