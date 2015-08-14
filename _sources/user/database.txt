Database
===============

The database is handle through the use of two components :

RESTBuilder-database (`doc <../restbuilder/restbuilder.database.html>`_)

    Within that component is found the connexion to the database. That connexion is created at the
    initialisation of that object using informations found in the file
    /config/restbuilder/database.py.

    .. todo :: What are the expected arguments ?

    This component has two uses :

    First, it provides SQLAlchemy sessions. Everytime a query must be run on the database, it is
    that component that will provide the session to run it.

    Secondly, it a Base handler. In SQLAlchemy, the creation / modification of the database is
    done throught the manipulation of the metadata of a Base object. In RESTBuilder, this object is
    located in this component.

RESTBuilder-records (`doc <../restbuilder/restbuilder.models.records.html>`_)
    This component is used to register the Record classes. It allows to keep track of wich Records
    already exists and allow other components to retrieve them.

    .. note :: There are work in progress to generate automatically the Record classes from their description. It is within this component that the generation would be done. Doing so could prevent for exemple the creation of the same table twice. (it creates a SQLAlechemy error to do so)
