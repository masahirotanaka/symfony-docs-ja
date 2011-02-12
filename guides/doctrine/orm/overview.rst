.. index::
   pair: Doctrine; ORM

O/Rマッパー
===

`Doctrine`_ は、データベース抽象化レイヤ（DBAL）の中でも最も強力なO/Rマッパーの一つです。Doctrineを使うと、意識することなくPHPオブジェクトを永続データとして扱うことができます。

.. 役立つヒント::

    Doctrine O/Rマッパーに関する詳細は、 `公式ドキュメント'_ を参照してください。

Doctrineを利用するには、まず :doc:`Doctrine DBAL
</guides/doctrine/dbal/overview>` を有効にし、適切な設定を行った後、O/Rマッパーを有効にします。最小限の設定で動作をするためには、エンティティを格納しているバンドルの名前を指定します。

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        doctrine.orm:
          mappings:
            HelloBundle: ~

    .. code-block:: xml

        <!-- xmlns:doctrine="http://www.symfony-project.org/schema/dic/doctrine" -->
        <!-- xsi:schemaLocation="http://www.symfony-project.org/schema/dic/doctrine http://www.symfony-project.org/schema/dic/doctrine/doctrine-1.0.xsd"> -->

        <doctrine:orm>
            <mappings>
                <mapping name="HelloBundle" />
            </mappings>
        </doctrine>

    .. code-block:: php

        $container->loadFromExtension('doctrine', 'orm', array(
            "mappings" => array("HelloBundle" => array()),
        ));

どのようなPHPクラスに対しても、Doctrineを通じてPHPオブジェクトが永続化されます。 ::

    // Sensio/HelloBundle/Entity/User.php
    namespace Sensio\HelloBundle\Entity;

    class User
    {
        protected $id;
        protected $name;

        public function getId()
        {
            return $this->id;
        }

        public function setName($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }
    }

.. 役立つヒント::

     エンティティを定義するとき、自分でGetterおよびSetterメソッドを記述しなくても
     ``doctrine:generate:entities`` コマンドを通じてDoctrineが作成することができます。
     ただし、次に説明するマッピング情報を事前に作成しておく必要があります。

Doctrineでクラス（Symfony2で言うところのエンティティ）を管理するには、アノテーション、XML、もしくはYAMLなどの方法でマッピング情報を記述しておく必要があります。

.. configuration-block::

    .. code-block:: php

        // Sensio/HelloBundle/Entity/User.php
        namespace Sensio\HelloBundle\Entity;

        /**
         * @orm:Entity
         */
        class User
        {
            /**
             * @orm:Id
             * @orm:Column(type="integer")
             * @orm:GeneratedValue(strategy="AUTO")
             */
            protected $id;

            /**
             * @orm:Column(type="string", length="255")
             */
            protected $name;
        }

    .. code-block:: yaml

        # Sensio/HelloBundle/Resources/config/doctrine/metadata/orm/Sensio.HelloBundle.Entity.User.dcm.yml
        Sensio\HelloBundle\Entity\User:
            type: entity
            table: user
            id:
                id:
                    type: integer
                    generator:
                        strategy: AUTO
            fields:
                name:
                    type: string
                    length: 50

    .. code-block:: xml

        <!-- Sensio/HelloBundle/Resources/config/doctrine/metadata/orm/Sensio.HelloBundle.Entity.User.dcm.xml -->
        <doctrine-mapping xmlns="http://doctrine-project.org/schemas/orm/doctrine-mapping"
              xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
              xsi:schemaLocation="http://doctrine-project.org/schemas/orm/doctrine-mapping
                            http://doctrine-project.org/schemas/orm/doctrine-mapping.xsd">

            <entity name="Sensio\HelloBundle\Entity\User" table="user">
                <id name="id" type="integer" column="id">
                    <generator strategy="AUTO"/>
                </id>
                <field name="name" column="name" type="string" length="255" />
            </entity>

        </doctrine-mapping>

.. 役立つヒント::

    YAMLやXMLでエンティティの定義を行うと ``doctrine:generate:entities``
    コマンドで自動的にEntityクラスが生成されます。

下記のコマンドで、データベースやスキーマに関連したメタデータ情報が作成されます。

.. code-block:: bash

    $ php app/console doctrine:database:create
    $ php app/console doctrine:schema:create

これで準備は整いました。エンティティを利用する際、Doctrineを通じて永続化が管理されます。 ::

    // Sensio/HelloBundle/Controller/UserController.php
    namespace Sensio\HelloBundle\Controller;

    use Sensio\HelloBundle\Entity\User;

    class UserController extends Controller
    {
        public function createAction()
        {
            $user = new User();
            $user->setName('Jonathan H. Wage');

            $em = $this->get('doctrine.orm.entity_manager');
            $em->persist($user);
            $em->flush();

            // ...
        }

        public function editAction($id)
        {
            $em = $this->get('doctrine.orm.entity_manager');
            $user = $em->find('HelloBundle:User', $id);
            $user->setBody('new body');
            $em->persist($user);
            $em->flush();

            // ...
        }

        public function deleteAction($id)
        {
            $em = $this->get('doctrine.orm.entity_manager');
            $user = $em->find('HelloBundle:User', $id);
            $em->remove($user);
            $em->flush();

            // ...
        }
    }

マッピング情報を変更したり、データベースのスキーマを変更した際にも、
既存データを維持しながらエンティティ情報を変更することができます。

たとえば ``User`` エンティティに新しいプロパティを追加した場合を考えます。 ::

    namespace Sensio\HelloBundle\Entities;

    /** @orm:Entity */
    class User
    {
        /** @orm:Column(type="string") */
        protected $new;

        // ...
    }

上記の通り修正を加えたら、下記のコマンドを通じてデータベースのスキーマを更新します。

    $ php app/console doctrine:schema:update

これで、データベースは更新され、新しいカラムがテーブルに追加されました。


.. _公式ドキュメント: http://www.doctrine-project.org/projects/orm/2.0/docs/en
.. _Doctrine:      http://www.doctrine-project.org
