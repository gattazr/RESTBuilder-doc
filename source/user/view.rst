View
===============

A View is a Cohorte component. Not requiring other components, the class definition should look like this :

.. code-block:: python

    @ComponentFactory("myapp.views.users_factory")
    @Provides("myapp.views.users")
    class UserView(AbstractView):
        """
        Functions used to display Users
        """

A View class should extend `AbstractView <../restbuilder/restbuilder.views.view.html>`_ or one of its
subclasses.

See a full View class : `UserView <#LINK>`_
