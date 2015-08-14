Model
===============

In CohorteAPI, a model is always composed of two classes : a Facilities and a Record.

* A **Record** is a class Mapping a Table in the database. Record objects can be loaded from the database or stored into it. Once created, Record classes are registered using the service RESTBuidler-records.

* A **Facilities** is a class used to create, load, save and delete Record objects. It is thus a factory with a few extra features. A Facilities is a Cohorte component. It is within Facilities that Record class they handle are defined.


.. note:: As of today, Record classes are manually created. However, there is work in progress to generate these classes from their definitions (see :ref:`tabledefinitions`).



.. _facilities:

Facilities
------------

A Facilities is a Cohorte component. Even though it is not mandatory, it is advised that Facilities
should extend `AbstractFacilities <../restbuilder/restbuilder.models.facilities.html>`_ or one of its
subclasses. And since it manages Records in the database, it requires the two database manager
components : RESTBuilder-database and RESTBuilder-records.

Therefore, the class definition should look like this :

.. code-block:: python

    @ComponentFactory("myapp.models.users_factory")
    @Provides("myapp.models.users")
    @Requires("_db", "restbuilder.database")
    @Requires("_records", "restbuilder.records")
    class UserFacilities(AbstractFacilities):
        """
        User Facilities
        """


Within Facilities classes, in functions decorated with @Validate, are defined :ref:`records`.


Inside that class could then be any functions you would like. Here are two examples :

.. code-block:: python

    def all(self):
        """
        Retrieve all users from the database

        :return: list of Users
        """
        wSession = self._db.session()
        wResult = wSession.query(self.getRecord()).all()
        wSession.close()
        return wResult


    def get(self, aUuid):
        """
        Retrieve the User with the given uuid from the database.
        The Role of that user will be retrieved at the same time

        :return: User
        """
        wSession = self._db.session()
        wResult = wSession.query(self.getRecord()).options(joinedload('role')).get(aUuid)
        wSession.close()
        return wResult

See a full Facilities class : `UserFacilities <#LINK>`_

.. todo:: Link to a file containing the full UserFacilities

.. _records:

Records
---------

Record classes must extends two classes :

* **Base** : Base is a class provided by function base() in the component CohorteAPI-database. By extending this class, the Record is automatically added to the database metadata. And therefore, the mapping of the database is handled automatically too. (Repoty to SQLAlchemy documentation for more details about the Base)
* `AbstractRecord <../restbuilder/restbuilder.models.record.html>`_ : An abstract class providing a couple usefull method and forcing the presence of the function __makeTableDef__() (see documentation about tabledefinitions_).

A Record class must always be created by the Facilities class handling it. And since it needs to
used some of the components requires, the class is created in a method with the decorate @Validate.
Here is an example of a class RecordUser found in the class UserFacilities.

.. code-block:: python

    @Validate
    def validate(self, context):
        """
        Register the record class associated to this Facility
        """
        wBase = self._db.base()
        wTableDef = self.makeTableDef()
        class RecordUser(wBase, AbstractRecord):
            """
            Mapping of the Users table
            """
            __tablename__ = 'Users'
            __tabledef__ = wTableDef

            uuid = Column(String(36), primary_key=True)
            email = Column(String(255), nullable=False)
            password = Column(String(255), nullable=False)

            def __repr__(self):
                return "<User(uuid=%s, email=%s, role=%r)>" % (self.uuid, self.email, self.role_id)



__makeTableDef__() is a function that creates a :ref:`tabledefinitions`



.. _tabledefinitions:

TableDefinitions
-----------------

In the module restbuilder.models.definitions are found 3 classes :

* TableDefinition
* ColumnDefinition
* RelationDefinition

Combined, it is possible to describe precisely a database Table. Therefore, using these classes, it
is possible to add a set of verification tests before sending request to the database.


.. note:: Since a TableDefinition describes a Table, just like a Record, there is work in progress to use these descriptions to define the RecordClasses

Here is the definition of the Table \`Users\` :

.. code-block:: python

    def __makeTableDef__(self):
        """
        Create the TableDefinition of the Users table

        :return: TableDefinition of the Users Table
        """
        wTable = TableDefinition('Users')
        wForeignRecord = self._roles.getRecord()
        wForeignName = '%s.id' % (wForeignRecord.__tablename__)
        wTable.addColumn(ColumnDefinition('uuid', String(36), aPrimaryKey=True))
        wTable.addColumn(ColumnDefinition('email', String(255), aNullable=False))
        wTable.addColumn(ColumnDefinition('password', String(255), aNullable=False))
        wTable.addColumn(ColumnDefinition('role_id', Integer, aForeignKey=wForeignName))
        wTable.addRelation(RelationDefinition('role', wForeignRecord))
        return wTable
