---
title: 設定 SSL 連線能力，以安全地連線到適用於 MySQL 的 Azure 資料庫
description: 有關如何適當設定「適用於 MySQL 的 Azure 資料庫」及相關聯應用程式以適當使用 SSL 連線的指示
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: f7346d5f40e0fe7dd4dbe892e96549f7ff181cb2
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/09/2019
ms.locfileid: "65511017"
---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫
適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流加密，有助於抵禦「中間人」攻擊。

## <a name="step-1-obtain-ssl-certificate"></a>步驟 1：取得 SSL 憑證
從 [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) 下載要透過 SSL 與「適用於 MySQL 的 Azure 資料庫」伺服器通訊所需的憑證，並將該憑證檔儲存到本機磁碟機 (例如，本教學課程使用 c:\ssl)。
**針對 Microsoft Internet Explorer 和 Microsoft Edge：** 在下載完成後，請將憑證重新命名為 BaltimoreCyberTrustRoot.crt.pem。

## <a name="step-2-bind-ssl"></a>步驟 2：繫結 SSL
### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 透過 SSL 連線至伺服器
設定使 MySQL Workbench 安全地透過 SSL 連線。 從 [設定新連線] 對話方塊，瀏覽至 [SSL] 索引標籤。在 [SSL CA 檔案:] 欄位中輸入 **BaltimoreCyberTrustRoot.crt.pem** 的檔案位置。 
![儲存自訂的圖格](./media/howto-configure-ssl/mysql-workbench-ssl.png) 對於現有連線，您可以透過在 [連線] 圖示上按一下滑鼠右鍵，然後選擇 [編輯] 將 SSL 繫結。 然後瀏覽至 [SSL] 索引標籤上，並繫結憑證檔案。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 透過 SSL 連線至伺服器
有另一個繫結 SSL 憑證的方法，就是藉由執行下列命令來使用 MySQL 命令列介面。 

```bash
mysql.exe -h mydemoserver.mysql.database.azure.com -u Username@mydemoserver -p --ssl-mode=REQUIRED --ssl-ca=c:\ssl\BaltimoreCyberTrustRoot.crt.pem
```

> [!NOTE]
> 在 Windows 上使用 MySQL 命令列介面時，您可能會收到 `SSL connection error: Certificate signature check failed` 錯誤。 如果發生此狀況，請以 `--ssl` 取代 `--ssl-mode=REQUIRED --ssl-ca={filepath}` 參數。

## <a name="step-3--enforcing-ssl-connections-in-azure"></a>步驟 3：強制在 Azure 中使用 SSL 連線 
### <a name="using-the-azure-portal"></a>使用 Azure 入口網站
使用 Azure 入口網站，瀏覽適用於 MySQL 的 Azure 資料庫伺服器，然後按一下 [連線安全性]。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線] 設定，然後按一下 [儲存]。 Microsoft 建議一律啟用 [強制使用 SSL 連線] 設定，以增強安全性。
![啟用 ssl](./media/howto-configure-ssl/enable-ssl.png)

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 Enabled 或 Disabled 值，以啟用或停用 **ssl-enforcement** 參數。
```azurecli-interactive
az mysql server update --resource-group myresource --name mydemoserver --ssl-enforcement Enabled
```

## <a name="step-4-verify-the-ssl-connection"></a>步驟 4：確認 SSL 連線
執行 mysql **status** 命令，確認您已使用 SSL 連線至 MySQL 伺服器︰
```dos
mysql> status
```
藉由檢閱輸出確認連線已加密，顯示結果應類似：**SSL:使用中的編碼器是 AES256-SHA** 

## <a name="sample-code"></a>範例程式碼
若要從您的應用程式透過 SSL 對「適用於 MySQL 的 Azure 資料庫」建立安全連線，請參閱下列程式碼範例：

### <a name="php"></a>PHP
```php
$conn = mysqli_init();
mysqli_ssl_set($conn,NULL,NULL, "/var/www/html/BaltimoreCyberTrustRoot.crt.pem", NULL, NULL) ; 
mysqli_real_connect($conn, 'mydemoserver.mysql.database.azure.com', 'myadmin@mydemoserver', 'yourpassword', 'quickstartdb', 3306, MYSQLI_CLIENT_SSL);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}
```
### <a name="php-using-pdo"></a>PHP （使用 PDO）
```phppdo
$options = array(
    PDO::MYSQL_ATTR_SSL_CA => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
);
$db = new PDO('mysql:host=mydemoserver.mysql.database.azure.com;port=3306;dbname=databasename', 'username@mydemoserver', 'yourpassword', $options);
```
### <a name="python-mysqlconnector-python"></a>Python (MySQLConnector Python)
```python
try:
    conn=mysql.connector.connect(user='myadmin@mydemoserver', 
        password='yourpassword', 
        database='quickstartdb', 
        host='mydemoserver.mysql.database.azure.com', 
        ssl_ca='/var/www/html/BaltimoreCyberTrustRoot.crt.pem')
except mysql.connector.Error as err:
    print(err)
```
### <a name="python-pymysql"></a>Python (PyMySQL)
```python
conn = pymysql.connect(user = 'myadmin@mydemoserver', 
        password = 'yourpassword', 
        database = 'quickstartdb', 
        host = 'mydemoserver.mysql.database.azure.com', 
        ssl = {'ssl': {'ca': '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'}})
```
### <a name="ruby"></a>拼音
```ruby
client = Mysql2::Client.new(
        :host     => 'mydemoserver.mysql.database.azure.com', 
        :username => 'myadmin@mydemoserver',      
        :password => 'yourpassword',    
        :database => 'quickstartdb',
        :ssl_ca => '/var/www/html/BaltimoreCyberTrustRoot.crt.pem'
    )
```
### <a name="golang"></a>Golang
```go
rootCertPool := x509.NewCertPool()
pem, _ := ioutil.ReadFile("/var/www/html/BaltimoreCyberTrustRoot.crt.pem")
if ok := rootCertPool.AppendCertsFromPEM(pem); !ok {
    log.Fatal("Failed to append PEM.")
}
mysql.RegisterTLSConfig("custom", &tls.Config{RootCAs: rootCertPool})
var connectionString string
connectionString = fmt.Sprintf("%s:%s@tcp(%s:3306)/%s?allowNativePasswords=true&tls=custom",'myadmin@mydemoserver' , 'yourpassword', 'mydemoserver.mysql.database.azure.com', 'quickstartdb')   
db, _ := sql.Open("mysql", connectionString)
```
### <a name="javajdbc"></a>JAVA(JDBC)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mysql://%s/%s?serverTimezone=UTC&useSSL=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```
### <a name="javamariadb"></a>JAVA(MariaDB)
```java
# generate truststore and keystore in code
String importCert = " -import "+
    " -alias mysqlServerCACert "+
    " -file " + ssl_ca +
    " -keystore truststore "+
    " -trustcacerts " + 
    " -storepass password -noprompt ";
String genKey = " -genkey -keyalg rsa " +
    " -alias mysqlClientCertificate -keystore keystore " +
    " -storepass password123 -keypass password " + 
    " -dname CN=MS ";
sun.security.tools.keytool.Main.main(importCert.trim().split("\\s+"));
sun.security.tools.keytool.Main.main(genKey.trim().split("\\s+"));

# use the generated keystore and truststore 
System.setProperty("javax.net.ssl.keyStore","path_to_keystore_file");
System.setProperty("javax.net.ssl.keyStorePassword","password");
System.setProperty("javax.net.ssl.trustStore","path_to_truststore_file");
System.setProperty("javax.net.ssl.trustStorePassword","password");

url = String.format("jdbc:mariadb://%s/%s?useSSL=true&trustServerCertificate=true", 'mydemoserver.mysql.database.azure.com', 'quickstartdb');
properties.setProperty("user", 'myadmin@mydemoserver');
properties.setProperty("password", 'yourpassword');
conn = DriverManager.getConnection(url, properties);
```

### <a name="net-mysqlconnector"></a>.NET (MySqlConnector)
```csharp
var builder = new MySqlConnectionStringBuilder
{
    Server = "mydemoserver.mysql.database.azure.com",
    UserID = "myadmin@mydemoserver",
    Password = "yourpassword",
    Database = "quickstartdb",
    SslMode = MySqlSslMode.VerifyCA,
    CACertificateFile = "BaltimoreCyberTrustRoot.crt.pem",
};
using (var connection = new MySqlConnection(builder.ConnectionString))
{
    connection.Open();
}
```

## <a name="next-steps"></a>後續步驟
檢閱[「適用於 MySQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)後面的各種應用程式連線能力選項
