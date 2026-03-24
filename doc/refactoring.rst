Refactoring opportunities
=========================

This page records candidate refactoring opportunities found in the codebase and
the refactoring types that can address them. It is intended as a starting point
for maintenance work and should evolve as TODO/FIXME notes are resolved.

Opportunities in the current codebase
-------------------------------------

* **Deferred generator attribute initialization**
  (``astroid/bases.py``: ``Generator.special_attributes`` TODO). The generator
  model is assigned at runtime because ``GeneratorModel`` depends on
  ``raw_building``. Consider introducing a factory or lazy initialization helper
  to remove class-level mutation from ``__init__`` and make the dependency
  explicit.
* **Circular import workaround**
  (``astroid/objects.py``: TODO about ``node_classes``/``objects``). The manual
  base-class reassignment is a workaround for a circular import. Extract shared
  types into a small module or apply dependency inversion to break the cycle.
* **Utility extraction in node comparisons**
  (``astroid/nodes/node_classes.py``: TODO to move ``_to_literal``). The
  ``_to_literal`` helper is a general-purpose conversion utility and can be
  moved into ``astroid.util`` to reduce coupling inside ``node_classes``.
* **API consolidation for child lookup**
  (``astroid/nodes/node_ng.py``: FIXME about merging ``child_sequence`` and
  ``locate_child``). The overlap suggests consolidating the lookup behavior in
  a single helper to reduce duplicated traversal logic.
* **Search path handling in module utilities**
  (``astroid/modutils.py``: FIXME ignoring ``search_path``). Path resolution can
  be isolated in a dedicated helper and covered with focused tests to clarify
  precedence rules and reduce branching inside the main functions.

Refactoring types to consider
-----------------------------

* **Extract function/method**: Isolate a block of logic into a named helper to
  simplify control flow and enable unit testing.
* **Extract class or module**: Move a cohesive set of functions or data into a
  new class/module to reduce file size and clarify ownership.
* **Move function/method**: Relocate behavior to the module or class that owns
  the data it manipulates (e.g., moving utility helpers to ``astroid.util``).
* **Inline function/method**: Remove small indirections when they obscure the
  main logic and add no reuse value.
* **Consolidate duplicate logic**: Replace repeated control flow with shared
  helpers or a single dispatch mechanism.
* **Replace conditional with dispatch**: Use mapping tables or polymorphism in
  place of long ``if/elif`` chains when behavior depends on types or operators.
* **Break cyclic dependencies**: Introduce interfaces or small shared modules
  to remove import cycles while preserving API boundaries.
* **Simplify data flow**: Replace ad-hoc tuples or dictionaries with named
  structures (``dataclasses`` or ``NamedTuple``) for clarity.
