.. index::
   pair: Doctrine; Migrations

マイグレーションの概要
==========

データベース マイグレーションは、データベース抽象化レイヤの拡張機能の一つです。マイグレーション機能を使うと、新しいバージョンのデータベース スキーマを、安全で標準化された手法でデプロイすることができます。

.. 役立つヒント::

    データベース マイグレーション機能に関する詳細は、 `公式ドキュメント'_ を参照してください。


マイグレーション機能は、コンソールから提供される数種類のコマンドで提供されます。

.. code-block:: bash

    doctrine:migrations
      :diff     既存のデータベースと比較を行い、マイグレーションを生成します
      :execute  手動でマイグレーションのバージョンを1世代あげ/さげます
      :generate 空白のマイグレーションクラスを生成します
      :migrate  指定されたバージョンもしくは最新バージョンまでマイグレーションを実行します
      :status   マイグレーションのステータスを表示します
      :version  バージョン テーブルからマイグレーション バージョンを手動で追加/削除を行います

すべてのバンドルは自前でマイグレーション管理を行うため、上記のコマンドを使用する際は、どのバンドルを対象とするか指定します。たとえば、 ``HelloBundle`` のマイグレーション ステータスを確認するには、下記のコマンドを実行します。

.. code-block:: bash

    $ php app/console doctrine:migrations:status --bundle="HelloBundle"

     == Configuration

        >> Name:                                               HelloBundle Migrations
        >> Configuration Source:                               manually configured
        >> Version Table Name:                                 hello_bundle_migration_versions
        >> Migrations Namespace:                               Sensio\HelloBundle\DoctrineMigrations
        >> Migrations Directory:                               /path/to/symfony-sandbox/src/Bundle/HelloBundle/DoctrineMigrations
        >> Current Version:                                    0
        >> Latest Version:                                     0
        >> Executed Migrations:                                0
        >> Available Migrations:                               0
        >> New Migrations:                                     0

さて、マイグレーション作業を開始するには、最初に空白のマイグレーション クラスを生成します。

.. code-block:: bash

    $ php app/console doctrine:migrations:generate --bundle="HelloBundle"
    Generated new migration class to "/path/to/project/src/Sensio/HelloBundle/DoctrineMigrations/Version20100621140655.php"

.. 役立つヒント::

    ``doctrine:migrations:generate`` コマンドを実行する前に、 ``/path/to/project/src/Sensio/HelloBundle/DoctrineMigrations`` フォルダを作成する必要があるかもしれません。

マイグレーション クラスでは、下記のようなコードが生成されています。 ::

    namespace Sensio\HelloBundle\DoctrineMigrations;

    use Doctrine\DBAL\Migrations\AbstractMigration,
        Doctrine\DBAL\Schema\Schema;

    class Version20100621140655 extends AbstractMigration
    {
        public function up(Schema $schema)
        {

        }

        public function down(Schema $schema)
        {

        }
    }

``HelloBundle`` の ``status`` コマンドを実行すると、下記の通り実行可能なマイグレーションが1つあることを示します。

.. code-block:: bash

    $ php app/console doctrine:migrations:status --bundle="HelloBundle"

     == Configuration

       >> Name:                                               HelloBundle Migrations
       >> Configuration Source:                               manually configured
       >> Version Table Name:                                 hello_bundle_migration_versions
       >> Migrations Namespace:                               Sensio\HelloBundle\DoctrineMigrations
       >> Migrations Directory:                               /path/to/symfony-sandbox/src/Sensio/HelloBundle/DoctrineMigrations
       >> Current Version:                                    0
       >> Latest Version:                                     2010-06-21 14:06:55 (20100621140655)
       >> Executed Migrations:                                0
       >> Available Migrations:                               1
       >> New Migrations:                                     1

    == Migration Versions

       >> 2010-06-21 14:06:55 (20100621140655)                not migrated

これで ``up()`` および ``down()`` メソッドにマイグレーション用のコードを記述する事で、マイグレーションを実施できるようになります。

.. code-block:: bash

    $ php app/console doctrine:migrations:migrate --bundle="HelloBundle"

.. _公式ドキュメント: http://www.doctrine-project.org/projects/migrations/2.0/docs/en
