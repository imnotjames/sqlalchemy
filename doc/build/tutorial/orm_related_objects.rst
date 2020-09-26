.. |prev| replace:: :doc:`orm_data_manipulation`
.. |next| replace:: :doc:`further_reading`

.. include:: tutorial_nav_include.rst

.. _tutorial_orm_related_objects:

Working with Related Objects
=============================

In this section, we will cover one more essential ORM concept, which is
that of how the ORM interacts with mapped classes that refer to other objects.
In the section :ref:`tutorial_declaring_mapped_classes`, the mapped class
examples made use of a construct called :func:`_orm.relationship` that has not
been referred to often in this tutorial.  This construct essentially defines a
linkage between two different mapped classes, or from a mapped class to
itself, the latter of which is called a **self-referential** relationship.

To describe the basic idea of :func:`_orm.relationship`, first we'll review
the mapping in short form, omitting the :class:`_schema.Column` mappings
and other directives::

  from sqlalchemy.orm import relationship
  class User(Base):

      # ... __tablename__, Column mappings

      addresses = relationship("Address", back_populates="user")


  class Address(Base):

      # ... __tablename__, Column mappings

      user = relationship("User", back_populates="addresses")



Persisting relationships
------------------------

.. _tutorial_select_relationships:

content

Using Relationships in Queries
-------------------------------

content

.. _tutorial_joining_relationships:

Using Relationships to Join
^^^^^^^^^^^^^^^^^^^^^^^^^^^

The sections :ref:`tutorial_select_join` and
:ref:`tutorial_select_join_onclause` introduced the usage of the
:meth:`_sql.Select.join` and :meth:`_sql.Select.join_from` methods to compose
SQL JOIN clauses.   In order to describe how to join between tables, these
methods either **infer** the ON clause based on the presence of a single
unambiguous :class:`_schema.ForeignKeyConstraint` object within the table
metadata structure that links the two tables, or otherwise we may provide an
explicit SQL Expression construct that indicates a specific ON clause.

When using ORM entities, an additional mechanism is available to help us set up
the ON clause of a join, which is to make use of the :func:`_orm.relationship`
objects that we set up in our user mapping, as was demonstrated at
:ref:`tutorial_declaring_mapped_classes`. The class-bound attribute
corresponding to the :func:`_orm.relationship` may be passed as the **single
argument** to :meth:`_sql.Select.join`, where it serves to indicate both the
right side of the join as well as the ON clause at once::

    >>> print(
    ...     select(Address.email_address).
    ...     select_from(User).
    ...     join(User.addresses)
    ... )
    SELECT address.email_address
    FROM user_account JOIN address ON user_account.id = address.user_id

The presence of an ORM :func:`_orm.relationship` on a mapping is not used
by :meth:`_sql.Select.join` or :meth:`_sql.Select.join_from` if we don't
specify it; it is **not used for ON clause
inference**.  This means, if we join from ``User`` to ``Address`` without an
ON clause, it works because of the :class:`_schema.ForeignKeyConstraint`
between the two mapped :class:`_schema.Table` objects, not because of the
:func:`_orm.relationship` objects on the ``User`` and ``Address`` classes::

    >>> print(
    ...    select(Address.email_address).
    ...    join_from(User, Address)
    ... )
    SELECT address.email_address
    FROM user_account JOIN address ON user_account.id = address.user_id


Joining to Aliased targets / of_type()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

EXISTS forms / has() / any()
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^


Common Relationship Operators
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Loader Strategies
-----------------

Selectin Load
^^^^^^^^^^^^^^

Joined Load
^^^^^^^^^^^

Explicit Join + Eager load
^^^^^^^^^^^^^^^^^^^^^^^^^^^
