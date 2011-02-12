.. index::
   pair: Doctrine; DBAL

DBAL
====

Doctrineデータベース抽象化レイヤ（DBAL）は、 `PDO`_ の上位に位置し、直感的で柔軟性の高いAPIを通じて多くのRDBMSとの通信が行えます。

.. 役立つヒント::

    Doctrineデータベース抽象化レイヤに関する詳細は、 `公式Webサイト`_ を参照してください。
DBALを使うには、下記の通りデータベースの接続先などを設定します。

.. code-block:: yaml

    # app/config/config.yml

    doctrine.dbal:
        driver:   pdo_mysql
        dbname:   Symfony2
        user:     root
        password: null

これにより、 ``database_connection`` サービスを通じてDBALに接続することが可能です。 ::

    class UserController extends Controller
    {
        public function indexAction()
        {
            $conn = $this->get('database_connection');

            $users = $conn->fetchAll('SELECT * FROM users');
        }
    }

.. _PDO:           http://www.php.net/pdo
.. _公式Webサイト: http://www.doctrine-project.org/projects/dbal/2.0/docs/en
.. _Doctrine:      http://www.doctrine-project.org
