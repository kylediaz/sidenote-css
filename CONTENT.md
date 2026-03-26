---
title: "Forget Sidenotes, Use Margin Notes"
date: "2024-07-16"
template: "article.html"
---

The margins are where authors put their unnecessary details, winded tangents,
bad jokes, and other extraneous information. The best parts of an
article are often hidden away in these literary gutters.

Many of my favorite blog writers use sidenotes.<sup>1</sup> I was inspired to
use them for some time but found a lot of shortcomings. Luckily, there's an alternative
that I feel much more comfortable with --- margin notes. They're literally the same as
sidenotes but without a numbering system which actually makes a substantial difference.

<aside class="sidenote">
    <p>
      <sup style="margin-left: -1em">1</sup>
      This is what a sidenote looks like.
    </p>
</aside>

<aside class="shift-vertically" style="--shift-vertically: -6em">
    <p>
      This is a "margin note".
      Mobile readers are very confused right now.
    </p>
</aside>

Moving from sidenotes to margin notes will not only make you a better writer
but it will make your code better.

## A better reading experience

Sidenote markings are distracting.
Their signature calling cards<sup>2</sup> interrupt the flow of text.
When I read a post with sidenotes, I have to put in a conscious effort
to stop myself from automatically looking at the sidenote. Getting
rid of the symbol adds *so much* readability.

## Clarity of writing

Sidenotes are like cheat codes for writers.
When revising, we might have to delete some sentences or even entire paragraphs
to make our writing more focused. We have such a hard time deleting the
things we so passionately wrote.
Instead of having to delete stuff, we can instead just move them to a sidenote
verbatim!
No additional editing is needed, either! Very convenient, right?

Sidenotes are easy to write but are not so easy to read. The problem stems
from the way we write sidenotes. They're highly dependent on the text around
them, so if the reader wants to read a sidenote, they have to remember all its context, too.

Margin notes are only loosely related to the text they're next to.
They should be perfectly understandable even when read by themselves.
You might have to rewrite them a little so they could work as a standalone tweet, paragraph, or maybe even short chapter.
It may take more effort to develop margin notes in this way, but it will make your ideas a lot more clear.

<aside class="margin-note">
The best execution of margin notes I've seen is in
[Stop Stealing Sheep](https://static.googleusercontent.com/media/fonts.google.com/en//knowledge/stop_stealing_sheep.pdf).
Spiekermann's margin notes are like bonus chapters - fully developed self-contained
ideas and stories.
</aside>

## Tufte CSS hate

By far, the most popular way to add sidenotes to a website is [Tufte CSS](https://edwardtufte.github.io/tufte-css/)
or some derivative. I don't like it. It uses an inline HTML element that looks like this:

```html
This sentence ends in a <label for="sn-1">sidenote</label><input id="sn-1" type="checkbox"><span class="sidenote">Sidenote content.</span>
```

This is pretty verbose, right?
Even if you use a templating engine, it's still pretty awkward.

<pre><code>This sentence ends in a &#123;&#123;&lt; sidenote &quot;sidenote&quot;&gt;}}Sidenote content. More sidenote content. Even more sidenote content.&#123;&#123;&lt; /sidenote &gt;}} Huh, I lost my place in the text</code></pre>

Having your sidenote text and main content mixed together into giant paragraphs
when trying to write in markdown is just not fun.
It also messes with my formatters.

This might only matter for the writing experience but no one but you is reading the source HTML, right?
Once it's rendered on the page, it won't matter, right?

For the small fraction of your users who use screen readers, RSS readers, or whatever, it will matter.
[Even some of the more semantic versions of Tufte CSS](https://www.kooslooijesteijn.net/blog/sidenotes-without-js)
will awkwardly (sn: This is what the sidenote will look like to people using
alternative reading methods. It's no big deal for just a single sentence, but
yappers like me will put entire paragraphs in here. I hope you didn't lose your
train of thought) inject the sidenote content into your main text.

## A margin note implementation

I know you people. It has to be JavaScript-free, accessible, semantic,
and easy to use. I got you.

I use a grid layout to create a "main section"

```css
article.has-sidebar {
    display: grid;
    grid-template-columns:
        [main-start] auto [main-end sidebar-start] 175px [sidebar-end];
}
```

Elements by default are in the "main" column, but sidebar content is put in the
sidebar just by using this sidebar class.
We can even make elements that span both columns.

```css
* {
    grid-column: main;
}

.sidebar, aside {
    grid-column: sidebar;
}

.full-width {
    grid-column: 1 / -1;
}
```

The HTML is also intuitive to write.

```html
<article class="has-sidebar"">
    <h1>Sidebar Example</h1>
    <p>
        I'm the first paragraph.
        My content shows up in the main section.
    </p>
    <div class="sidebar"">
        My content shows up in the sidebar, right
        next to the first paragraph.
    </div>
    <p>
        I'm the second paragraph.
        My content shows up in the main section.
    </p>
</article>
```

As the HTML shows, it should be easy for text-to-speech and other alternate
interfaces to understand.

On mobile devices, we can use CSS to collapse the layout so everything is on
one column.

Best of all, this uses a pretty common and powerful layout pattern.
[Ryan Mulligan](https://ryanmulligan.dev/blog/layout-breakouts/) shows how
to use it for all sorts of things.

## Collapsible margin notes

This simple solution is great on its own, but it has one flaw --- on mobile,
your asides are no longer on the sides, and now your readers might have these
massive secondary blocks of text they have to scroll past.
On mobile, we can make asides collapsed by default, and you can press them to
expand.

In my opinion, making asides not collapsable is perfectly fine. [It's like the
asides you'd see in actual books.](https://doc.rust-lang.org/book/ch03-02-data-types.html#integer-overflow)
It's always a matter of taste.

Anyway, the best way to make asides collapsible is by using the `<details>` <small>HTML</small> tag.

```html
<details>
    <summary>Click me</summary>
    Hi, I'm an example details element
</details>
```

Which looks like this:

<details>
    <summary>Click me</summary>
    Hi, I'm an example details element
</details>

We can make it so on desktop, it acts like a normal margin note, but on mobile it
will collapse to a normal `<details>` element.

```scss
details.sidebar {
    @media (min-width: $mobile-breakpoint) {
        grid-column: sidebar;
        summary {
            display: none;
        }
    }
}
```

You also have to add the open attribute (as in `<details open>`) so it's in
the open state by default.

This results in that →

<details class="collapsible-demo" open>
    <summary>A title for the aside</summary>
    <p>
      I'm an aside
    </p>
</details>

There is one minor issue: if you don't use JavaScript, it will have to be open by default
on mobile too.
I feel on mobile, it should be collapsed by default.
Good thing I'm not too anti-JavaScript.

```javascript
if (isMobileDevice()) {
    const sidebarDetails = document.querySelectorAll('details.sidebar');
    sidebarDetails.forEach(function(detail) {
        detail.removeAttribute('open');
    });
}
```

If you're *really* anti-JS, you can make this CSS-only using a similar technique
as Tufte-CSS. I wouldn't recommend it though because it won't be nearly as semantic,
it might not look that good on alternate readers, and most importantly it's ugly code.
Implementing it is also an exercise left to the reader.

## Additional tips

Here's another hot take: use justified text with margin notes. Yeah, justified
text runs the risk of [creating inconsistent gaps between words](https://practicaltypography.com/justified-text.html)
and it's supposedly less readable, but have you considered that justifying your
text will create a beautiful gap between the two columns of text?
Nothing could be sexier than two parallel columns of text, in my opinion.

## Still lacking

Margin notes still have many flaws.

1. Secondary text should by their very definition not draw too much attention, but marginal notes *literally* stick out on the page.
   I try to make them less pronounced by making their font smaller, greying them out, and giving them plainer fonts but I still can't help but get distracted by them.

2. There's an argument that you should forgo using footnotes, sidenotes, margin
   notes, and other "notes" altogether. Footnotes are used
   extensively in Law, and there's one Justice who is famous for being against footnotes
   because he believes not using them makes his writing "clearer and more readable..., with fewer ambiguities".

   Imagine reading a research paper with no footnotes. This is that guy.

   “[E]ither a point is sufficiently significant to make, in which case it
   should be in the text, or it is not, in which case, don’t make it.”
   ([source](https://www.nytimes.com/1995/07/28/us/in-justice-breyer-s-opinion-a-footnote-has-no-place.html))

3. Writing margin notes is unergonomic. I really want a WYSIWYG editor for them.
