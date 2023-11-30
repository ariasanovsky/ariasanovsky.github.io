+++
title = "Proof and Learning"
date = "2023-11-29"

[taxonomies]
tags = ["meditations", "machine learning", "proof", "research", "career"]
+++

*Meditations from my transition from math academia to industry. Mentions my current labor-of-love, an [experimental optimization crate](https://github.com/ariasanovsky/azdopt/).*

## Proof, a social game

### In academia

It's 2023 and Terence Tao is [formalizing theorems with software](https://mathstodon.xyz/@tao/111287749336059662).
Mathematics [will probably change](https://www.youtube.com/watch?v=oDyviiN4NVo&t=1020s) when simulation becomes a viable medium to faciliate rigorous, open-ended problem-solving.

![Output from a Lean REPL which contradicts a simple, false mathematical claim, from Terence Tao's blog](/self/tao-lean-code.png)

Even mathematical proof, with formally stated theorems, is a social game.
For minor results, convince your team, yourself, and the reviewers of a few claims.
Do this by sandwiching formal arguments in a document prepared by code in an archaic scripting language.
For major results, convince the community, too.

Mathematical papers go like this.
Expose key ideas.
Factor prose from proof.
Invert the creative meander into an enumerated syllogism.
Encapsulate grimy lemmas so the poor reader can take a break.

It's a synthesis of *communication* and *formalism*.
A necessary, but exhausting labor for [the academic who is always expected to produce output](https://en.wikipedia.org/wiki/Publish_or_perish).
But sometimes [communication fails](https://www.scientificamerican.com/article/math-mystery-shinichi-mochizuki-and-the-impenetrable-proof/).
And sometimes the [formalism falls apart](https://www.ams.org/notices/201304/rnoti-p418.pdf).

Computers have been helping us since the [Appel-Haken proof](https://en.wikipedia.org/wiki/Four_color_theorem#Proof_by_computer) of the $4$-Color Theorem.
[It took a decade](https://en.wikipedia.org/wiki/Four_color_theorem#Early_proof_attempts) to retract the flawed, computer-less proofs.

What role will computers play in math in the coming years?

### In industry

I have a practical question:

> How does a researcher with a decade of experience developing *ad hoc* solutions with whatever-tool-it-takes find a team that values her creativity, growth, and passion for collaborate problem-solving?

![Tensorboard plots representing decreasing cost functions and loss functions`](/self/tb-out.png)


In industry, the burden of proof is different.
Like math, it's not all about *formalism*.
My first "resume" was a stack of 200 pages of published research and a 7-page CV. *Ha!*

To *communicate*, build projects, push commits, and make some pretty graphics.
Find community, collaborate, and get lucky.
At least, that's what I'm trying.
Like math, it's not always going to work.
Like math, it mostly doesn't.
So try new things, but take care of yourself too.

## Learning from states and actions

### As an academic

![An Obsidian canvas depicting a flower-shaped arrangement of 5 code-blocks. `ActionSequence`, `ActionMultiset`, `OrderedActionSet`, and `ActionSet` are connected through the traits `ActionsNeverRepeat` and `ActionOrderIndependent`](/self/symmetry.png)

My best ideas come from data.

I used to write so much [SageMath](https://www.sagemath.org/).
So many Jupyter notebooks full of dynamic programs, print statements, plots.
All for my mathematical experiments.
Lost to the time before I understood version control.

My learning loop consisted (partly) of:

- (perform a search) trying to prove from first principles
- (visualize data) plotting computational evidence
- (make predictions) extrapolating from datasets and plots
- (calculate loss) making enthusiastic claims
- (backprop gradients) sheepishly email teammates about my miscalcuations

This worked *surprisingly* well, and made collaborators and mentors chuckle.
After all, there is no closed-form solution to [explore-exploit dilemma](https://en.wikipedia.org/wiki/Exploration-exploitation_dilemma).
Everyone has their own approach.
Try everything.
Measure what you can.

### In deep reinforcement learning

I like reinforcement learning, but I know I'm only scratching the surface.
My [data-parallel optimization crate](https://github.com/ariasanovsky/azdopt/) has an MVP and probably needs a new name.
Maybe `azdo`.

It has some helper traits which form a $2\times 2$ grid of derived `impl`s.
If you squint, you'll find this block in the top-left corner of the [$12$-fold way](https://en.wikipedia.org/wiki/Twelvefold_way#Formulas) table.
An ergonomic way to reduce combinatorial explosion in the size of Monte Carlo search trees.

![A screenshot of the 4-by-3 grid of formulas from the cited Wikipedia reference for the 12-fold way, with the 2-by-2 top-left corner highlighted](/self/12-fold-corner.png)

What I love about implementing these algorithms is simple:

> unlike pure math, progress can be clearly measured

Searches produce concrete structures, plottable data, prediction functions, and numerical loss.
You can even adjust behavior in real-time.

So far:

- It refutes [Conjecture 2.1](https://github.com/ariasanovsky/azdopt/issues/42#issuecomment-1826050504) (previously refuted by Wagner with the cross entropy method) in only one minute.
- It's riddled with [a list of desired features](https://github.com/ariasanovsky/azdopt/issues?q=is%3Aissue) I have publicly [externalized](https://en.wikipedia.org/wiki/Extended_mind_thesis#%22The_Extended_Mind%22).

Offline progress continues in the privately externalized backburner (read: [Obsidian](https://obsidian.md/) canvases).

## For now

I have been:

- chewing on a novel problem space
- taking a healthy break
- visiting loved ones
- skimming papers and blogs
- making new connections.

I'm optimistic.
If that changes, I have a practical job search optimization algorithm I can refactor.
