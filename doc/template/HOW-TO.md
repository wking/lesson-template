## Setup

I start cloning our example

    $ git clone -b gh-pages https://github.com/swcarpentry/lesson-example.git

and add some remote repositories

    $ cd lesson-example
    $ git remote add template https://github.com/r-gaia-cs/swc-lesson-template.git
    $ git remote add template-upstream https://github.com/swcarpentry/lesson-template.git
    $ git remote -v
    origin  https://github.com/swcarpentry/lesson-example.git (fetch)
    origin  hhttps://github.com/swcarpentry/lesson-example.git (push)
    template        https://github.com/r-gaia-cs/swc-lesson-template.git (fetch)
    template        https://github.com/r-gaia-cs/swc-lesson-template.git (push)
    template-upstream       https://github.com/swcarpentry/lesson-template.git (fetch)
    template-upstream       https://github.com/swcarpentry/lesson-template.git (push)

I use our example because it is easy to debug but if you want to use
`origin` as your fork of the lessons you are working is totally OK.

## Preparation

Every time before I start to hack the template I update my local copy using

    $ git fetch origin
    $ git fetch template
    $ git fetch template-upstream

Once my local copy is updated I create a feature branch,
**starting from the last commit on `lesson-example`**, where I will work.

  $ git checkout -b example-centering-image origin/gh-pages

## Hacking

For this how-to, suppose that I want to add the class `img-responsive` to every `img` elements of our lessons.
One **quickly and dirty** way to do that is change `Makefile` with

    $ git diff
    diff --git a/Makefile b/Makefile
    index 4d38850..ff49ca9 100644
    --- a/Makefile
    +++ b/Makefile
    @@ -55,6 +55,7 @@ motivation.html : motivation.md _layouts/slides.revealjs Makefile
            --filter=tools/filters/id4glossary.py \
            $(INCLUDES) \
            -o $@ $<
    +       sed -i 's/^<img /<img class="img-responsive" /g' $@

     # Pattern to convert R Markdown to Markdown.
     %.md: %.Rmd $(R_CHUNK_OPTS)

To avoid losing my work, I save it in the staging area:

    $ git add -u

Now I compile the HTML files:

    $ make -B preview

And verify the changes.

    $ git diff
    diff --git a/01-one.html b/01-one.html
    index 7622e55..1fc0d45 100644
    --- a/01-one.html
    +++ b/01-one.html
    @@ -56,7 +56,8 @@ program</code></pre>
     </div>
     <p>or an image from the <code>figs</code> directory:</p>
     <div class="figure">
    -<img src="fig/example.svg" title="this is the image&#39;s alt text" alt="this is the image&#39;s title" /><p class="caption">this is the image's title</p>
    +<img class="img-responsive" src="fig/example.svg" title="this is the image&#39;s alt text" alt="this is the image&#39;s title" />
    +<p class="caption">this is the image's title</p>
     </div>
     <div id="challenge-title" class="challenge panel panel-success">
     <div class="panel-heading">

This is exactly what I want. Great!!! I can now commit my changes.

    $ git commit -m 'Add img-responsive class to img'
    [example-centering-image a9cc86d] Add img-responsive class to img
     1 file changed, 1 insertion(+)

I will also save the change to the HTML file.

    $ git add -u
    $ git commit -m 'Update HTML'
    [example-centering-image 1ebf8c3] Update HTML
     1 file changed, 2 insertions(+), 1 deletion(-)

## Copying the change to template

If you have taken the Git lesson at a Software Carpentry workshop, you should be comfortable with what you see until now.
What I need to do is copy my change at `Makefile` to [template](https://github.com/swcarpentry/lesson-template) using `git cherry-pick`. If you are trying to remember it from our workshop, don't panic because we don't teach it but you will discovery that it is very simple to use.

Before anything else I need to know the ID of the commit I want to copy, the one where I changed `Makefile`.

    $ git log --oneline -3
    1ebf8c3 Update HTML
    a9cc86d Add img-responsive class to img
    5426365 Merge pull request #1 from gvwilson/updating-docs

The commit that I want is `a9cc86d`. I will save the ID for a moment until I create a new branch starting from the template (**instead of the example**):

    $ git checkout -b template-centering-image template-upstream/gh-pages

Now I can "copy and paste" my change, using the commit ID, and for that I use

    $ git cherry-pick a9cc86d
    [template-centering-image 2004c4f] Add img-responsive class to img
     Date: Sat Apr 4 18:19:26 2015 -0300
     1 file changed, 1 insertion(+)

I can check that the commit was copied

    $ git log --oneline -2
    2004c4f Add img-responsive class to img
    cf25427 Merge pull request #218 from wking/trailing-newlines

The commit ID change but this is OK and is desired.
Now I can upload my changes

    $ git push template template-centering-image

And [create a pull request](https://github.com/swcarpentry/lesson-template), [like this one](https://github.com/swcarpentry/lesson-template/pull/219).
