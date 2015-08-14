Controller
===============

A Controller is a Cohorte component. It is both a servlet and a request handler. Therefore, the class can be divided in two.

According to the architecture described `here <architecture.html>`_, the controller must have a
reference on both the model and the view. Therefore, it requires the two components. And since it is
a servlet, a couple extra properties are set :

The definition of a Controller should look like this.

.. code-block:: python

    @ComponentFactory("myapp.controllers.users_factory")
    @Provides(specifications='pelix.http.servlet')
    @Property('_path', 'pelix.http.path', "/users")
    @Property('_reject', pelix.remote.PROP_EXPORT_REJECT, ['pelix.http.servlet'])
    @Requires("_model", "myapp.models.users")
    @Requires("_view", "myapp.views.users")
    class UserController(AbstractController):
        """
        A component that provides the /users servlet
        """

A Controller class should extend `AbstractController <../restbuilder/restbuilder.controllers.controller.html>`_ or one of its subclasses.

See a full Controller class : `UserController <#LINK>`_
