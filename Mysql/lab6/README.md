# MySQL Enterprise Edition Lab6
MySQL Enterprise Encryption

# Preparation
[Create New Instance](../lab1#create-new-mysql-instance)

## Started with mysqld_safe
```
sudo -u mysql /lab/mysql/bin/mysqld_safe --defaults-file=/lab/mysql_home01/my.cnf 2>&1 &>/dev/null &

```

# Install the firewall
```
mysql -S /lab/mysql_home01/mysqld.sock 
```
mysql>
```
CREATE FUNCTION asymmetric_decrypt RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION asymmetric_derive RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION asymmetric_encrypt RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION asymmetric_sign RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION asymmetric_verify RETURNS INTEGER SONAME 'openssl_udf.so';
CREATE FUNCTION create_asymmetric_priv_key RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION create_asymmetric_pub_key RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION create_dh_parameters RETURNS STRING SONAME 'openssl_udf.so';
CREATE FUNCTION create_digest RETURNS STRING SONAME 'openssl_udf.so';

```
## Creating the keys
mysql>
```
SET @algo = 'RSA'; -- can be 'DSA' or 'DH' instead;
SET @key_len = 1024; -- Minimum key length in bits; make larger for stronger keys;
SET @priv = CREATE_ASYMMETRIC_PRIV_KEY(@algo, @key_len);
SET @pub = CREATE_ASYMMETRIC_PUB_KEY(@algo, @priv);

select @priv;
select @pub;
```
## Encrypt with private and decrypt with public for authenticity proof
mysql>
```
SET @ciphertext = ASYMMETRIC_ENCRYPT(@algo, 'Lab secret text', @priv);
SET @plaintext = ASYMMETRIC_DECRYPT(@algo, @ciphertext, @pub);
select @plaintext;
```
## Encrypt with public and decrypt with private for hiding data
mysql
```
SET @ciphertext = ASYMMETRIC_ENCRYPT(@algo, 'Lab secret text', @pub);
select @ciphertext;
select hex(@ciphertext);
SET @ciphertext = ASYMMETRIC_ENCRYPT(@algo, 'Lab secret text', @pub);
select hex(@ciphertext);

SET @plaintext = ASYMMETRIC_DECRYPT(@algo, @ciphertext, @priv);
select @plaintext;

shutdown; 
\q

```


Next: [ MySQL Enterprise Monitor](../lab7) 