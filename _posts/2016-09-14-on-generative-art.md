---
layout: post
category : art
tagline: "Musings about the philosophy of expression in a computational world"
tags : [generative art, coding, expression]
---
{% include JB/setup %}

![2016-09-12-22-35-mosaic-misty-city-julia-ACZ](/assets/img/on-generative-art/2016-09-12-22-35-mosaic-misty-city-julia-ACZ.png)
```
frame {
  *{{n=29} x -.5 y -.5}
    (n)*{x 1/n} (n)*{y 1/n}
      R4{ shading misty_city_by_sea   // this shading takes parameters col0, SAT
          col0 julia(x/5, y/5, 0, .75) / 256
        { D=4;  // recursion level for "R4" structure
          SAT=julia(x*2.5, y*2.75, 0, .75) / 256
            *julia((x+.1)*2.5, (y+.1)*2.75, 0, .75) / 256
            *julia((x+.2)*2.5, (y-.3)*2.75, 0, .75) / 50
        }
        s 1.2/n
        PUSH sub
      }
}
sub .3 { sub{{SAT=SAT-.1}} }
sub 3 { sub{col0 (col0+.1)} }
sub { SQUARE{s 1.2} }
```

A friend linked [an excellent essay](http://inconvergent.net/generative/)
from Anders Hoff, a Norwegian generative artist,
knowing that I've been working on some
[generative software](https://github.com/pvto/konte-art)
for some time now.

I am an avid drawer, so generative art has a peculiar fascination for me –
not just the pleasure of beautiful and specially charged things
that bear this impulse to craft so intrinsic in art,
but also something extra in there,
in *generating* and *coding* for dramatic visual result,
in *being able to*.

Now I'm quite aware that generative art can't match
the subtle expressive power of a human hand.
For me, none of the generative work I've seen
has been anywhere near to Picasso's drawings –
script-drawn human figures simply aren't aesthetically that stimulating yet.
Though I liked Hoff's growing structures very much,
they do not epitomize human experience in such a way as
Picasso's line drawings or Tove Jansson's sheets of Moomin graphic do.

Generative art, in comparison to, say, oil painting,
is peculiar in its computer-based work methods.
What these methods do extremely well is to make some repetitive tasks easier.
In a way they enable experimentation with
such repetitive form that would be too much a waste of time in
a pre-computer world.

In doing generative art,  *Art* becomes a new kind of control problem,
and it now highlights and enjoys the inner structures
and some mathematical and progressive aspects of what we see and imagine.
I like the faint glow of universality –
it definitely has educational and intellectual fascination.

I think that generative art in some beautiful,
unfractured, and hypothetical form
perfectly retains two disparate levels of perception:
the material and visual qualities of a reproducible end result,
and then the creation script, an intellectual journey
marked by heightened interaction with its spatial environment, for the mind.

In this way generative art is like Plato's universe,
at the same time material and visible
and then observable for the mind in its inner essence.

Generative art will, I hope,
defy reduction to market/cult value or some such similar thing,
because it so uncompromisingly maintains these
two separate layers of perfection:
the resulting art and the generating art,
creating a dynamic framework for their playful interaction.

I think generative art then has one intrinsic fallacy too,
and it is so that it is easy to reduce to a bunch of tools,
and after that it can be reduced, or get reduced,
to a dull story and a string of boring habits.
But if you retain the relation of the script and the result,
and keep them together,
I think generative art can have its magic cake and eat it too
in the future.

This is exactly what happens to me all the time, this reduction process,
cage-locking, habit-forming familiarisation of technique, a reduction
of the sphere of interest, fixation into observed and discovered forms,
fading of the heightened spatial interaction from the picture.
And I try to keep my head above the water and fly.
