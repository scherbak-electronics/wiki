# Setup Snippets
Remove `DEFINER` from dump using sed on MacOS
```shell
sed -i '' 's/DEFINER=[^*]*\*/\*/g' db_dump.sql
```
<br>

Standard database cleanup using SQL
```sql
UPDATE core_config_data SET value = 'http://jgm2.test/' WHERE path = 'web/unsecure/base_url';
UPDATE core_config_data SET value = 'https://jgm2.test/' WHERE path = 'web/secure/base_url';
UPDATE core_config_data SET value = 'jgm2.test' WHERE path = 'web/cookie/cookie_domain';
UPDATE core_config_data SET value = '' WHERE path = 'twofactorauth/duo/secret_key';
UPDATE core_config_data SET value = '' WHERE path = 'twofactorauth/duo/application_key';
SELECT * FROM core_config_data WHERE path LIKE '%smile%';
SELECT * FROM core_config_data WHERE path LIKE '%elastic%';
```