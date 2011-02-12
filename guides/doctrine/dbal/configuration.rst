.. index::
   single: Configuration; Doctrine DBAL
   single: Doctrine; DBAL configuration

設定
=============

下記の例は、接続先としてMySQLデータベースを利用する場合のものとなります。

.. code-block:: yaml

    # app/config/config.yml
    doctrine.dbal:
        driver:   pdo_mysql
        dbname:   Symfony2
        user:     root
        password: null

DoctrineBundleの設定では、Doctrineドライバーのすべての定義をXMLもしくはYAML表記で記述できます。これらの記述の詳細は、 `DBALドキュメント`_ を参照してください。

また、Symfony関連の設定も追加でサポートしています。下記の設定は、指定可能なすべての項目となります。

.. configuration-block::

    .. code-block:: yaml

        doctrine.dbal:
            dbname:               database
            host:                 localhost
            port:                 1234
            user:                 user
            password:             secret
            driver:               pdo_mysql
            driver_class:         MyNamespace\MyDriverImpl
            options:
                foo: bar
            path:                 %kernel.data_dir%/data.sqlite
            memory:               true
            unix_socket:          /tmp/mysql.sock
            wrapper_class:        MyDoctrineDbalConnectionWrapper
            charset:              UTF-8
            logging:              %kernel.debug%
            platform_service:     MyOwnDatabasePlatformService

    .. code-block:: xml

        <!-- xmlns:doctrine="http://www.symfony-project.org/schema/dic/doctrine" -->
        <!-- xsi:schemaLocation="http://www.symfony-project.org/schema/dic/doctrine http://www.symfony-project.org/schema/dic/doctrine/doctrine-1.0.xsd"> -->

        <doctrine:dbal
            dbname="database"
            host="localhost"
            port="1234"
            user="user"
            password="secret"
            driver="pdo_mysql"
            driver-class="MyNamespace\MyDriverImpl"
            path="%kernel.data_dir%/data.sqlite"
            memory="true"
            unix-socket="/tmp/mysql.sock"
            wrapper-class="MyDoctrineDbalConnectionWrapper"
            charset="UTF-8"
            logging="%kernel.debug%"
            platform-service="MyOwnDatabasePlatformService"
        />

さらに、DIコンテナ用パラメータを通じたクラスを指定することも可能です。

.. code-block:: yaml

    parameters:
        doctrine.dbal.logger_class: Symfony\Bundle\DoctrineBundle\Logger\DbalLogger
        doctrine.dbal.configuration_class: Doctrine\DBAL\Configuration
        doctrine.data_collector.class: Symfony\Bundle\DoctrineBundle\DataCollector\DoctrineDataCollector
        doctrine.dbal.event_manager_class: Doctrine\Common\EventManager
        doctrine.dbal.events.mysql_session_init.class: Doctrine\DBAL\Event\Listeners\MysqlSessionInit
        doctrine.dbal.events.oracle_session_init.class: Doctrine\DBAL\Event\Listeners\OracleSessionInit
        doctrine.dbal.logging: false

複数の接続を設定したい場合は、下記の通り ``connections`` キーのなかに必要な項目を追加します。

.. code-block:: yaml

    doctrine.dbal:
        default_connection:       default
        connections:
            default:
                dbname:           Symfony2
                user:             root
                password:         null
                host:             localhost
            customer:
                dbname:           customer
                user:             root
                password:         null
                host:             localhost

複数の接続が定義されている場合、 ``$this->get('doctrine.dbal.[接続名]_connection)`` と参照することで接続の取得できます。

    class UserController extends Controller
    {
        public function indexAction()
        {
            $defaultConn1 = $this->get('doctrine.dbal.connection');
            $defaultConn2 = $this->get('doctrine.dbal.default_connection');
            // $defaultConn1 === $defaultConn2

            $customerConn = $this->get('doctrine.dbal.customer_connection');
        }
    }

.. _DBALドキュメント: http://www.doctrine-project.org/projects/dbal/2.0/docs/en