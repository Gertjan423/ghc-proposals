Notes on reStructuredText - delete this section before submitting
==================================================================

The proposals are submitted in reStructuredText format.  To get inline code, enclose text in double backticks, ``like this``.  To get block code, use a double colon and indent by at least one space

::

 like this
 and

 this too

To get hyperlinks, use backticks, angle brackets, and an underscore `like this <http://www.haskell.org/>`_.


Explicit Specificity in Kinds
==============

.. author:: Gert-Jan Bottu
.. date-accepted:: Leave blank. This will be filled in when the proposal is accepted.
.. ticket-url:: Leave blank. This will eventually be filled with the
                ticket URL which will track the progress of the
                implementation of the feature.
.. implemented:: Leave blank. This will be filled in with the first GHC version which
                 implements the described feature.
.. highlight:: haskell
.. header:: This proposal is `discussed at this pull request <https://github.com/ghc-proposals/ghc-proposals/pull/0>`_.
            **After creating the pull request, edit this file again, update the
            number in the link, and delete this bold sentence.**
.. contents::

This proposal introduces new syntax, allowing users to specify whether types are
required, specified or inferred in data / class / type declarations.
This enables more fine grained control (and convenience) over how users should
instantiate the types through visible type application.

Consider for instance the following data type
::
 data D1 a = K1
which corresponds to the following kind for ``D1``
::
 forall {k}. k -> Type
Note that ``D1`` is in fact kind polymorphic, but with an **inferred** kind
variable ``k``. This means that users shouldn't instantiate the kind, and can
just pass in the required type as ``D1 Int`` for example.

Now consider the following data type, where we annotate the type variable ``a``
with its kind
::
 data D2 (a :: k) = K2
which corresponds to the following kind for ``D2``
::
 forall k. k -> Type
Note that in this case, the kind variable ``k`` is **specified**, meaning that
users can choose to explicitly pass in the kind for ``k`` as well as the type
for ``a``. For example, users could either write ``D2 Int`` and ``D2 Type Int``.

Finally, let's say we want to explicitly bind the kind ``k``, which would look
as follows
::
 data D3 k (a :: k) = K3
If we now ask GHC for the kind of ``D3``, we get
::
 forall k -> k -> Type
Note that the kind variable ``k`` has become **required**, meaning that users
have to instantiate the kind before they can instantiate the type.
All of this is already possible with current versions of GHC.

This proposal introduces new syntax to specify whether a type variable should be
required, specified or inferred when explicitly binding a variable.
A previous proposal which has recently been implemented,
`proposal 99 <https://github.com/ghc-proposals/ghc-proposals/blob/master/proposals/0099-explicit-specificity.rst>`_,
already has this feature in type signatures, data constructor
declarations, pattern synonyms etc.
This work makes for a natural extension of proposal 99, by extending its scope
to include its syntax in data, class and type declarations.

More concretely, proposal 99 enables syntax like 
::
 typeRep :: forall {k} (a :: k). Typeable a => TypeRep a
Note the braces surrounding the ``k``. This new form of type variable binder
marks the variable as inferred.
This syntax makes perfect sense in data, class and type declarations as well,
and so we propose to enable the following syntactic forms
::
 data D4 k    (a :: k) = K4
 data D5 @k   (a :: k) = K5
 data D6 @{k} (a :: k) = K6
which should result in the following kind for ``D4``, ``D5`` and ``D6``
respectively
::
 forall k -> k -> Type
 forall k.   k -> Type
 forall {k}. k -> Type
Note that the kind variable ``k`` in these examples is **required**,
**specified** and **inferred** respectively.

Motivation
----------
Give a strong reason for why the community needs this change. Describe the use
case as clearly as possible and give an example. Explain how the status quo is
insufficient or not ideal.

A good Motivation section is often driven by examples and real-world scenarios.


Proposed Change Specification
-----------------------------
Specify the change in precise, comprehensive yet concise language. Avoid words
like "should" or "could". Strive for a complete definition. Your specification
may include,

* BNF grammar and semantics of any new syntactic constructs
* the types and semantics of any new library interfaces
* how the proposed change interacts with existing language or compiler
  features, in case that is otherwise ambiguous

Note, however, that this section need not describe details of the
implementation of the feature or examples. The proposal is merely supposed to
give a conceptual specification of the new feature and its behavior.

Examples
--------
This section illustrates the specification through the use of examples of the
language change proposed. It is best to exemplify each point made in the
specification, though perhaps one example can cover several points. Contrived
examples are OK here. If the Motivation section describes something that is
hard to do without this proposal, this is a good place to show how easy that
thing is to do with the proposal.

Effect and Interactions
-----------------------
Detail how the proposed change addresses the original problem raised in the
motivation.

Discuss possibly contentious interactions with existing language or compiler
features.


Costs and Drawbacks
-------------------
Give an estimate on development and maintenance costs. List how this effects
learnability of the language for novice users. Define and list any remaining
drawbacks that cannot be resolved.


Alternatives
------------
List existing alternatives to your proposed change as they currently exist and
discuss why they are insufficient.


Unresolved Questions
--------------------
Explicitly list any remaining issues that remain in the conceptual design and
specification. Be upfront and trust that the community will help. Please do
not list *implementation* issues.

Hopefully this section will be empty by the time the proposal is brought to
the steering committee.


Implementation Plan
-------------------
(Optional) If accepted who will implement the change? Which other resources
and prerequisites are required for implementation?

Endorsements
-------------
(Optional) This section provides an opportunty for any third parties to express their
support for the proposal, and to say why they would like to see it adopted.
It is not mandatory for have any endorsements at all, but the more substantial
the proposal is, the more desirable it is to offer evidence that there is
significant demand from the community.  This section is one way to provide
such evidence.
