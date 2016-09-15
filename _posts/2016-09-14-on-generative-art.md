---
layout: post
category : art
tagline: "Musings about the philosophy of expression in a computational world"
tags : [generative art, coding, expression]
---
{% include JB/setup %}

![2016-09-14-22-34-mosaic-misty-city-julia-AAF](/assets/img/on-generative-art/2016-09-14-22-34-mosaic-misty-city-julia-AAF.png)

<pre class="smaller-text">
frame {
  *{ {n=29} x -.5 y -.5}
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
</pre>

A friend linked [an excellent essay](http://inconvergent.net/generative/)
from Anders Hoff, a Norwegian generative artist,
knowing that I've been working on some
[generative software](https://github.com/pvto/konte-art)
for some time now.

I am an avid drawer, so generative art has a peculiar fascination for me –
not just the pleasure of beautiful and specially charged things,
but also something extra in there,
in *generating* and *coding* for dramatic visual result.
An aesthetic drive is intrinsic in art,
but this flavour is more specialized.

Now I think that generative art can't match
the subtle expressive power of a human hand.
None of the generative work I've seen
has been anywhere near to Picasso's drawings –
script-drawn human figures simply aren't aesthetically that stimulating yet.
Though I liked Hoff's growing structures very much,
they do not epitomize human experience in such a way as
Picasso's line drawings or Tove Jansson's sheets of Moomin graphic do.

Generative art, in comparison to, say, oil painting,
is special due to its computer-based work methods.
What these methods do extremely well is make some repetitive tasks easier.
In a way they enable experimentation with
form that would be too boring and inhumane – too big waste of time without a computer.

*Art* becomes a new kind of control problem,
and it now highlights and enjoys some new inner structures
of what we see and imagine.
I like the faint glow of universality –
it definitely has educational and intellectual fascination.

I think that generative art should ideally
retain two disparate levels of perception:
the material and visual qualities of a piece of art,
and then its creation script and the intellectual journey
that led to the end result.
It possibly should bear marks of that intense
interaction with the spatial environment that the
visible work manifests.

![2016-09-14-22-39-mosaic-misty-city-julio-AAW](/assets/img/on-generative-art/2016-09-14-22-39-mosaic-misty-city-julio-AAW.png)

<pre class="smaller-text">
DEF n 29
DEF im .91
fov {z -1.1 x -.5/n y -.5/n}

frame {
  *{x -.5 y -.5}
  (n)*{x 1/n} (n)*{y 1/n}
    R4{ shading misty_city_by_sea
        col0 julia(x/5,y/5, 0, im)/256
      { D=4;
        SAT=julia(x*2.5,y*2.75, 0, im)/256
           *julia((x+.1)*2.5,(y+.1)*2.75, 0, im)/256
           *julia((x+.2)*2.5,(y-.3)*2.75, 0, im)/50
      }
        s 1.2/n
        PUSH sub
    }
}
sub .3 { sub{{SAT=SAT-.1}} }
sub 3 { sub{col0 (col0+.1)} }
sub { SQUARE{s 1.2} }
</pre>

Generative art will, I hope,
create a dynamic framework for the playful interaction
of concrete and abstract,
of visible and tangible on one hand
and ideal and structural on the other.

I think generative art then has one intrinsic fallacy.
It is quite easy to reduce in your mind to a bunch of tools,
and after that it becomes a dull story of boring habits.
But if you retain the relation of the script and the result,
I think generative art can have its magic cake and eat it too.

This is exactly what happens to me all the time, this reduction process,
cage-locking, habit-forming familiarisation of technique, a reduction
of the sphere of interest, fixation into observed and discovered forms,
fading of the heightened spatial interaction from the picture.
And I try to keep my head above the water and fly.