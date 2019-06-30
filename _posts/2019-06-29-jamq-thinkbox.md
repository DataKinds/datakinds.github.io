---
layout: post
title: Jamq Thinkbox
date: 2019-06-29 20:24 -0700
---

Jamq (**J**ust **A**synchronous **M**essages and **Q**ueues) is a language I've been tossing back and forth in my mind. Here's what I'm thinking about:

```
# learn X in Y minutes, where X = Jamq

#{

Jamq is centered around the idea of the primary queue. Think of the primary queue as the 
post office: regardless of where you are, you can access your post office and send,
recieve, and handle message and communications.

The primary queue has a few important properties:

##########
# Queues #
##########

* It is asynchronous: multiple producers can send messages onto the queue at the same
  time. Multiple consumers can read messages off of the queue at the same time. It 
  is up to the programmer to impose an order that this happens, else it will happen
  order independently.
  
* It is typed: messages placed on the queue have specific types, and consumers reading
  off of the queue can only react to specific types of messages.

So, let's see a few different ways to send messages to the queue.

#}

# This puts an unboxed `3 : Num` into the queue.
3;

# This does the same thing, using the `queue` verb.
queue primary 3;

# This does the same thing, with an optional type annotation
queue primary (3 : Num);

# Since `queue` is a common operation, we can omit it and just list the queue we 
# are queueing to.
primary (3 : Num);

#{

So, what do we do with the number that's there? We can register a handler for messages
of type `Num`. Up until now, I've been loose with the terminology "handler", "consumer",
and etc. Now, I will define strict terminology which I will use for the rest of the
document.

###############
# Terminology #
###############

* Primary queue: as discussed above.

* Secondary queue: as discussed below.

* Handler: Anything that handles messages (plain handler, box, tube, cap, custom 
  handlers).

* Plain handler: A handler that has no special functionality.

* Rule: The rules governing a handler. Can be custom, or can be predefined as per box,
  cap, or tube.

* Box: A handler that takes one message, changes its type, and places it back into 
  the queue. 

* Tube: A handler that takes one message, changes its value but not its type, and places 
  it back into the queue. Tubes do not get called on a value more than once.

* Cap: A handler that takes one message, and eventually discards it.

Let's not dawdle around any more. Let's do something with the `3 : Num` that we've placed 
in the queue.

#}

# First, we define a custom `Cap`, that responds to `Num`s. Note that nouns are 
# capitalized, and verbs aren't. Convention, convention.

Cap (A : Num) -> NumPrinter {
	# This cap automatically destructs the `Num` after it is consumed, because of
	# the nature of it being a `Cap`.
	
	# Let's queue this to the IO module's secondary queue. More on modules and 
	# queues later.
	IO.print A
	
	# And we destruct A automatically as per `Cap`'s rules.
}

# Note that 
#     Cap (A : Num) -> NumPrinter { ... }
# can be written more explicitly as
#     Cap (A : Num) -> NumPrinter primary { ... }
# to specifiy that NumPrinter watches the primary queue.

# Let's register `NumPrinter` to the primary queue. This overrides the queue that
# is annotated on the `NumPrinter` definition.
register primary, NumPrinter;

# Or, we could just register to the default queue. This will register the handler
# to whatever queue is annotated, or the primary queue otherwise.
register NumPrinter;

# So, now, pushing 3 to the primary queue:
3;
# will cause NumPrinter to activate, and print
# =>  3
# to the standard output.

########################
# Multi Param Handlers #
########################

#{

Handlers can take multiple arguments, which get sent to the handler in the order
that they are recieved in the queue. Other types can intersperse between the types
handled by the handler, as long as the handled types exist in the queue in the
correct order.

This means that a single queue can act as a sort of "meta-queue", in that it allows
multiple types to queue up independently. Let's see this in action.

#}

# Here's a handler that waits for two numbers, then adds them.
# Note that arbitrary handlers actually _remove_ the values they match against
# from their queues.

Handler (A : Num), (B : Num) -> Adder {
	# The queue that the handler is registered to can be referred to using the 
	# keyword `registered`.
	queue registered (A + B);
}

# Then,

register primary Adder;
3; # Nothing happens
2; # 3, 2 are dequeued, 5 is queued back

########################################
# Creating queues and other fun things #
########################################

# This is an introduction to creating queues and attributes. Attributes aren't always
# super useful, as modules can be parametrized over values. But, they can come in 
# handy at times when parametrization would be too obtuse. 

# This is an empty queue that exists at a global level. Note the use of the new
# verb `new`:
new (Q : Queue);

# This is a number that exists at a global level. Calling `new` is optional if 
# `set` is used instead. It'll set the attribute to some sensible value.
new (N : Num);
set N, 3;

# That's equivalent to 
set (N : Num), 3;

####################
# Secondary queues #
####################

# Secondary queues are queues that are defined in modules. They are either public,
# which means they can be touched by the outside world, or private, which means
# that they can't.

# Let's define a module M with a few different secondary queues and attributes.
module M {
	# Can be accessed within M only.
	private (Q : Queue);
	
	# Accessed as M.R
	public (R : Queue);
	
	# Accessed as M.N
	# Can be set using `set M.N 1234`
	public (N : Num);
}

# If a module only has one private queue, it can be accessed using the keyword
# `secondary`, just as the `primary` keyword can be used anywhere.

# Here's an interesting tidbit: `IO` can just be defined as a module with a bunch
# of public queues. `IO.print` is a public queue.

###########
# Modules #
###########

#{

Modules are a way to contain code. They can be parametrized over types a-la 
O'Caml.

Modules *usually* don't need to be initialized, but if they're parametrized,
then we have to initialize them in order to provide the types or values to parametrize
them over.

Let's build a module to log debug messages to the terminal for some specific 
type `T`.

#}

# First, we establish the module parametrized over type `T`, and a string `Msg`.
# Note the familiarity of this syntax.

Module (T : Type), (Msg : String) -> DEBUG {
	# In here, we'll define a handler that prints out whatever value was `Tube`'d
	# in along side our custom message.
	
	Tube (A : T) -> DebugHandler {
		IO.print Msg; IO.print A;
		
	    # We'll introduce a new verb here: `rule`. This passes an object back up
		# through the rules defined by the kind of handler that we're in. In this
		# case, we're in a `Tube` handler, so `rule` must take a value of type `T`
		# and will simply put it back on the queue that the handler was registered to.
		rule A;
	}
}

# And let's initialize this module. The special thing about modules is that
# they can actually be interacted with as normal every-day queues. So let's 
# queue a type into the `DEBUG` queue, which is essentially a Box
# which modifies the type object passed in.

queue DEBUG, Num, "This number is: ";
# or
DEBUG Num, "This number is: ";

# But how do we get the initialized Module out? We could use a handler on the `DEBUG`
# queue to handle type `Module`, but that's too much. The verb `set` can 
# be used in the situation that the queue only contains one object after running the
# expression on the right hand side. The syntax is as follows:

set MYDEBUG, [DEBUG Num, "This number is: "];

# Remember the `[]` syntax, as it'll come back later. We can use it anytime the situation
# above arises: when we put things into an empty queue, then get one singular thing
# back out of the queue. Brackets allow us to pass that one single thing to a verb.

# Now, we can use `MYDEBUG` as a normal module.

register primary MYDEBUG;
100; # This prints "This number is:
                    100"

###################
# Custom messages #
###################

#{

Here's our first full project!

You can define distinct types for custom messages. Let's define a little calculator
using messages and handlers. First, let's define messages that represent actions
that our calculator can take.

#}

# A message with one untyped element `toBePrinted`.
Message Output { toBePrinted }

# A couple message with two typed elements representing computations.
Message Add {
	A : Num,
	B : Num
}
Message Mul {
	A : Num,
	B : Num
}

# And the associated handlers:
Box (AddMsg : Add) -> AddHandler primary {
	# Let's create a new local attribute using the `local` verb.
	# Think about how we used `public`, `private`, and `new`.
	local (N : Output);
	
	set N.toBePrinted, (AddMsg.A + AddMsg.B);
	rule N;
}
Box (MulMsg : Add) -> MulHandler primary {
	local (N : Output);
	set N.toBePrinted, (AddMsg.A * AddMsg.B);
	rule N;
}
Cap (OutputMsg : Output) -> OutputHandler primary {
	IO.print OutputMsg.tobePrinted;
}

# And now, let's use it! And check out the shorthand for queueing
# custom messages. This could be used above, as well.
queue primary [Add 3, 10]; # Prints "13".
queue primary [Mul 10, 4]; # Prints "40".

# See that bracket notation coming back? Think about it for a little bit.

############################
# Custom handler templates #
############################

# ??????
# Not sure how to provide the definitions for `rule` or etc.

##################
# Asynchronicity #
##################

How is code executed?

As far as I can figure out right now, I think that only separate modules should
be automatically parallelized. Seperate handlers _might_ be parallelizable,
as long as they handle distinct types due to the whole meta-queue-esq property.

```


