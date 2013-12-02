---
layout: post
title: "Designing an artificial intelligence: An introduction to Bayesian statistics"
date: 2013-12-01 21:59
comments: true
categories: 
---

> I don't have anything in mind to post this week, so I'm reposting this article I wrote for myself in 2010 after deep-diving into Bayesian statistics and finally making sense of it (at least in my own mind). I hope it makes sense to you too!

<h3>
First principles
</h3>

<p>Drawing conclusions is relatively straightforward when you're sure of what
you know.  <em>A</em> is true, and <em>A</em> implies <em>B</em>, therefore <em>B</em> is true.  Here <em>A</em> and <em>B</em> are
<strong>propositions</strong>, statements which are (in principle) either true or false,
even if we don't know which of the two they are.
</p>

<p>However, most decisions that we need to make depend
on propositions that can't be judged true or false given the data at
hand.  Is it going to rain today?  Will I get sick if I don't get a flu
vaccine?  Will the stock market go up or down in six months? If we knew the
answers to these questions ahead of time, even approximately, it would
allow us to make better decisions.
</p>

<p>Is it possible then, to design an artificial intelligence (an AI) to help us
reason about uncertain propositions in an optimal way?  In order
to begin, we would first need to come up with a relatively satisfying
definition of "optimal".  At first one might hope for an AI that
that provides some sort of guarantee of the correctness of the answers it provides.  As far
as I know this hasn't been done (after all, we are dealing
with <em>uncertain</em> propositions), but there has been progress towards
satisfying the following four design goals:
</p>

<ol>
<li>
<strong>Universality</strong>: we would like our AI to be able to provide answers to a
wide variety of questions.
</li>

<li>
<strong>Simplicity</strong>: as a practical matter, a simple design is easier to understand and implement.
</li>

<li>
<strong>Agrees with our intuition</strong>: I can't be more precise about this until I get
into the details of our AI's internals, but don't worry, I will be very
specific when I invoke this requirement.
</li>

<li>
<strong>Consistency</strong>: we would like for our AI to give us the same answer
every time we ask it a specific question.
</li>
</ol>

<p>
Of the four of these criteria, the last one, consistency, is perhaps the
least important.  If our AI isn't universal it won't be able to provide
an answer to many of our questions; if isn't simple it also won't be
able to provide many answers, because it will be too difficult to implement
and use; and if it doesn't agree with our intuition then the
answers it does give us will appear absurd or arbitrary.  However, a lack of
consistency in the answers it provides doesn't result in any similar
disadvantage.
</p>

<p>
This may seem strange, because normally if we are designing a machine to
evaluate propositions then a lack of consistency is an indication of a
serious error.  For example, if you had a calculator that gave a different
answer every time you typed in "2308 * 5982" then (even not knowing the
answer yourself) you would be able to tell there is something wrong with the
calculator, since there is only one number equal to 2308 * 5982, and it can
be determined based on the given data.
</p>

<p>
However, imagine that the purpose of your calculator was to determine the
mass of Saturn based on a series of astronomical observations. In that case,
we can't say that something is wrong with the calculator because it doesn't
output the same answer every time we input the same exact data, since
(in the context of this example) the data given to the calculator is not sufficient to
determine the actual mass of Saturn.
</p>

<p>
However, there is at least one practical reason to desire consistency;
debugging our AI will be easier when we know exactly what response to expect
from it based on a given input.  So although I will not stress the
importance of consistency, I will mention in passing how we can ensure that
our AI will provide consistent answers, at least for debugging purposes.
</p>

<h3>
AI internals
</h3>

<p>When human beings reason about uncertain propositions, we tend to attach
weights or "levels of belief" to the different propositions under
consideration.  For example, if there are clouds in the sky one might think
of the proposition "it is going to rain today" as having more weight than
the negation of that statement, "it is not going to rain today".
</p>

<p>
This suggests one way to design our AI. Given an uncertain proposition <em>A</em>
which the AI must decide on and some background information <em>I</em> related to <em>A</em>,
the AI could assign a real number to <em>A</em>, called <em>B(A | I)</em>, which you can
pronounce "the AI's belief in proposition <em>A</em> given background information <em>I</em>".
The AI could also assign a real number to the negation of <em>A, &minus;A</em>, which we
would call <em>B(&minus;A | I)</em>.
</p>

<p>
If <em>B(A | I) ≥ B(&minus;A | I)</em>, then the AI's output will be "<em>A</em> is true", and
otherwise "<em>A</em> is false".  As you can see, the AI displays a preference for
the original proposition <em>A</em> when the beliefs are equal. We could just as
easily have had things the other way, but since the AI must make some
decision we might as well choose one arbitrarily (here you can see the
consistency issue already beginning to rear its head).
</p>

<p>
It is important to understand the following point: the AI's level of belief
<em>B(A | I)</em> in the proposition <em>A</em> is only a description of the AI's internal
state; it has no significance beyond the internals of our AI.  It is not
a statement about the true nature of the proposition <em>A</em>, nor is it a statement about the long
term frequency of <em>A</em> being true in repeated trials, whatever that would mean.
It is merely a description of the AI's internals; any attempt to attach
additional significance to it will only result in confusion.
</p>

<p>
It is also worth mentioning that this is not the only way to design such an
AI; instead of assigning one real number to a given proposition we could
assign several, or we could imagine some other mechanism which doesn't
involve numbers at all.  This idea merely suggests itself to us because it
is simple, and it is similar to the way we humans think.
</p>

<p>
The only question now is, how will our AI determine <em>B(A | I)</em> in a way that
satisfies our original four design goals?  To do this, I will state just six
rules that our AI will follow as it assigns belief values to propositions.
Each rule will be justified by one or more of our design goals, and all of
the rules together will be enough to fully determine how our AI will form
beliefs.  We are already familiar with the first rule:
</p>

<p>
D1) Given a proposition <em>A</em> and some background information <em>I</em>, the AI will assign a real number
B(A | I) to <em>A</em>.  This is similar to the way that humans reason about uncertain
propositions.
</p>

<p>
D2) There exists some real number <em>T</em> such that <em>B(A | I) &le; T</em> for every <em>A</em> and
I.  If <em>A</em> is known to be true, then the AI should assign <em>B(A | I) = T</em>.
</p>

<p>
Logically this is not essential, and in fact it doesn't follow from any of
our design goals.  The reason for this rule is merely practical: because of storage constraints we cannot actually create an AI
with unbounded belief values.
</p>

<p>
D3) The assignment of beliefs is compatible with everyday logic.
</p>

<p>
This stems from our third design goal; the AI must assign beliefs to propositions in a way that
agrees with our intuition. For example, if <em>A</em> and A' are equivalent
propositions then the AI should assign equal levels of belief to <em>A</em> and <em>A'</em>,
so that <em>B(A | I) = B(A' | I)</em>.  There are some other requirements here, but
they are all similarly straightforward (see <a href="http://ksvanhorn.com/bayes/Papers/rcox.pdf">Kevin van Horn</a> for details).
</p>

<p>
D4) There is a nonincreasing function <em>S</em> such that <em>S(B(A | I)) = B(&minus;A | I)</em>.
Define <em>F = S(T)</em>.
</p>

<p>
This also is meant to agree with our intuition.  In simple English, it means
that if some new data causes the AI to assign a higher level of belief to A,
then it's level of belief in the negation of <em>A</em> should not increase as
a result.  We could require that the AI's belief in <em>-A</em> should
actually decrease rather than just not increase, but we are trying to be as
unrestrictive as possible while still satisfying our original design goals.
</p>

<p>
D5) There are no intervals contained in <em>(F, T)</em> that contain belief values
which are not possible for our AI to have (the required statement is
actually somewhat stronger than
this. See <a href="http://ksvanhorn.com/bayes/Papers/rcox.pdf">van Horn</a> for the details).
</p>

<p>
The primary justification for this requirement is that it appeals to our
intuition, since we humans do not think of levels of belief as being finite
and discrete, we think of them as being continuous.  If we reject D5 then we
are asserting that there is no "middle ground" between some two levels of belief
in a proposition--an awkward assertion in light of everyday experience.
</p>

<p>
D6) There is a strictly increasing, continuous function <em>F</em> such that
<em>B(A, C | I) = F(B(A | C, I) · B(C | I))</em> for all propositions <em>A</em> and <em>C</em> with
background information <em>I</em>. Here <em>B(A, C | I)</em> is a real number
that represents "the
AI's belief in both <em>A</em> and <em>C</em>, given I".
</p>

<p>
Once again, the argument for this requirement is that it appeals to our
common sense.  One's belief in a both <em>A</em> and <em>C</em> should depend on both one's
belief in C, and one's belief in <em>A</em> given that <em>C</em> is true.  See
<a href="http://omega.math.albany.edu:8008/JaynesBook.html">Jaynes</a> or
<a href="http://ksvanhorn.com/bayes/Papers/rcox.pdf">van Horn</a> for
further arguments in favor of this requirement.
</p>

<h3>Bayes' Rule</h3>

<p>
That's it, we have set out six rules for how our AI should operate
internally, and that is enough to completely determine the algorithm that
our AI will use to calculate belief values for nearly every kind of
proposition it could encounter. For more information on this result, known
as Cox's
Theorem, <a href="http://omega.math.albany.edu:8008/JaynesBook.html">van
    Horn's paper</a> is the best resource I've found.
</p>

<p>
Without going into the proof, it turns out that that the function B can be
rescaled so that <em>B(T) = 1</em> and <em>B(F) = 0</em>, which agrees with the usual mapping of 1 to
true propositions, and 0 to false propositions.  It can also be shown that <em>B</em>
can be formulated so that the following two rules hold:
</p>

<p>
<strong>The sum rule</strong>:
<em>B(&minus;A | I) = 1 &minus; B(A | I)</em>
</p>

<p>
<strong>The product rule</strong>:
<em>B(A, C | I) = B(A | C, I) · B(C | I)</em>
</p>

<p>
Now if we switch the roles of <em>A</em> and <em>C</em> in the product rule, we have
</p>

<p>
<em>B(C, A | I) = B(C | A, I) · B(A | I)</em>
</p>

<p>
but <em>B(C, A | I) = B(A, C | I)</em> by D1, since (<em>A and C</em>) and (<em>C
and A</em>) are equivalent propositions, so we have that
</p>

<p>
<em>B(A | C, I) · B(C | I) = B(C | A, I) · B(A | I)</em>
</p>

<p>
Finally, by dividing both sides by <em>B(C | I)</em>, we have the Bayes'-Laplace Rule
(traditionally shortened to Bayes' Rule):
</p>

<p>
<strong>Bayes' Rule</strong>:
<center>
<img class="tex" alt="B(A \,|\, C,\, I) = {B(C\, |\, A,\, I) \cdot B(A \,|\, I)\over B(C\, |\, I)}" src="/images/2013-12-01/latex-image-1.png">
</center>
</p>

<p>
If we let <em>A</em> be our hypothesis, and <em>C</em> be the data we have collected
concerning the hypothesis, then we can see the true power of Bayes' rule.
<center>
<img class="tex" alt="B(hypothesis\, |\, data,\, I)\, =\, {B(data\, | \,hypothesis,\, I)\,\cdot\, B(hypothesis \,|\, I) \over B(data \,|\, I)}" src="/images/2013-12-01/latex-image-2.png">
</center>
</p>

<p>
The term <em>B(hypothesis | data, I)</em> is traditionally called the <strong>posterior</strong>,
since it describes our AI's belief in the hypothesis <em>after</em> viewing the
data.  The term <em>B(data | hypothesis, I)</em> is called the <strong>evidence</strong> or more
traditionally the <strong>likelihood</strong>; it assigns a number to the degree to which the
hypothesis is supported by the data.  <em>B(hypothesis | I)</em> is the <strong>prior</strong>; it
shows our AI's belief in the hypothesis <em>before</em> viewing the data.  <em>B(data |
I)</em> is simply a constant which does not depend on the hypothesis; it is
often ignored since we are usually only interested in choosing a good hypothesis.
</p>

<p>
One of the reasons that Bayes' Rule is so celebrated is because it precisely
describes the scientific method--the idea that we must use data to update
our beliefs about a hypothesis. From this simple equation our AI gains an
almost unlimited ability to learn and solve problems, because for almost every
problem we encounter, the process of finding a solution can be described as
"updating a hypothesis in light of the data".
</p>

<p>
It is worth reiterating an earlier point; there is no claim here that this
is the only way to reason about uncertain propositions, or even the best
way. It is merely a widely applicable, simple, and intuitively appealing
way for humans (and machines designed by humans) to evaluate uncertain propositions.  While
Bayes' Rule is elegant and highly appealing in its own right, it
could not be described as a universal law in any meaningful way; it is just
a highly idealized description of the way humans reason about
uncertain propositions.
</p>

<h3>A last word about consistency and correctness</h3>

<p>When our AI encounters new data it will update its beliefs using Bayes'
  Rule, but what sort of beliefs should our AI have before encountering any
  data at all?  In other words, how is our AI supposed to select a
  prior?</p>

<p>
  A common approach is to select prior beliefs in a way
  that expresses total ignorance (though what this means has been the subject of
  some debate).  To that end, several guidelines have
  been developed for selecting ignorant priors, such as the
  the <a href="http://en.wikipedia.org/wiki/Principle_of_indifference">Principle of Indifference</a> and the <a href="http://en.wikipedia.org/wiki/Principle_of_maximum_entropy">Principle of Maximum Entropy</a>.
</p>

<p>
 I would argue that the main advantage of these principles is that they keep our AI consistent, so that a pair of AI who have seen the
  same data will assign the same belief values (a useful feature for
  debugging purposes).  However, I haven't encountered an argument
  for why such guidelines give any real advantage in practice; we
  can't argue that the AI's belief assignments are more correct for
  having chosen a prior in one way rather than another.  This is part of
  a larger problem; there is no guarantee that our AI produces answers
  which reflect the real world at all.</p>

<p>What I just said may sound overly cynical, but I am only claiming that there has not <em>yet</em>
  been a proof that our  AI's beliefs reflect the real world in
  some way.  I am not claiming that there will <em>never</em> be such a proof.  A good argument
  in favor of our third design goal (intuitiveness) is that an AI
  which agrees with intuition stands a good chance of having some
  claim to correctness, since our intuitions have evolved along with us over millions of
  years of making decisions under uncertainty.</p>

<p>If you would like to learn more, rather than overwhelm you with a long list of additional reading
  material I will just recommend one short book, <a href="http://www.amazon.com/Data-Analysis-Bayesian-Devinderjit-Sivia/dp/0198568320/ref=sr_1_1?s=books&ie=UTF8&qid=1290438345&sr=1-1">Data Analysis: A Bayesian Tutorial</a>.  It is
  full of good examples of Bayes' Rule being put to use in real
  problems, and after reading it you should be able to understand most books
  and papers on Bayesian' methods, allowing you to continue to learn on your own.
</p>
