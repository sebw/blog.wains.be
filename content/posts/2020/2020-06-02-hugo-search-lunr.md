---
date: 2020-06-02
title: Build a search engine for your Hugo website
---

## From Mkdocs to Hugo

I moved this blog from Mkdocs to Hugo just yesterday.

Mkdocs has a great search engine by default but no RSS.

Hugo has RSS but most of the themes do not provide a search engine.

I like simple stuff and decided to go with [Etch](https://themes.gohugo.io/etch/) theme, which doesn't provide a search engine.

## Hugo doesn't have search engine

I decided to investigate my options and discovered that [Lunr](https://lunrjs.com/) could help me on the task.

Lunr is a client side javascript library that can search JSON file.

Basically, I just needed to figure out how to:

1. generate the JSON file
2. build a search page
3. make lunr search in the JSON and display the results

## Looking for inspiration

I'm not a javascript or client side expert.

I found two great blogs which explained how they implemented search using lunr, all with examples:

- [https://www.integralist.co.uk/posts/static-search-with-lunr/](https://www.integralist.co.uk/posts/static-search-with-lunr/)
- [https://www.josephearl.co.uk/post/static-sites-search-hugo/](https://www.josephearl.co.uk/post/static-sites-search-hugo/)

I fetched ideas from both blogs and came up with my own implementation which I think is simpler.

## Implementation

In your Hugo `config.toml` you must specify this:

```ini
[outputs]
  home = ["HTML", "RSS", "JSON"]
  page = ["HTML", "RSS"]
```

This doesn't do anything unless you drop a file under `./layouts/_default/index.json`:

```json
{{- $.Scratch.Add "index" slice -}}
{{- range .Site.RegularPages -}}
    {{- $.Scratch.Add "index" (dict "title" .Title "tags" .Params.tags "content" .Plain "href" .Permalink "date" ( .Date.Format "Jan 2, 2006" )) -}}
{{- end -}}
{{- $.Scratch.Get "index" | jsonify -}}
```

With both the config and this JSON template, Hugo will build an `index.json` file at the root of your site.

In my case the address is [https://blog.wains.be/index.json](https://blog.wains.be/index.json).

This is what the JSON file looks like.

![](https://blog.wains.be/images/lunr-index.png)

Now let's take care of the search page.

Under `content/search.md`, create a page with the form:

```markdown
---
title: Search
---

<div>
<p><input id="search" type="text" placeholder="Enter your query"></p>

<ul id="results"></ul>

<script src="/js/jquery-2.1.3.min.js"></script>
<script src="/js/lunr.js"></script>
<script>
  var lunrIndex,
      $results,
      documents;

  function initLunr() {
    // retrieve the index file
    $.getJSON("../index.json")
      .done(function(index) {
          documents = index;

          lunrIndex = lunr(function(){
            this.ref('href')
            this.field('content')

            this.field("title", {
                boost: 10
            });

            this.field("tags", {
                boost: 5
            });

            documents.forEach(function(doc) {
              try {
                // console.log(doc.href)
                this.add(doc)
              } catch (e) {}
            }, this)
          })
      })
      .fail(function(jqxhr, textStatus, error) {
          var err = textStatus + ", " + error;
          console.error("Error getting Lunr index file:", err);
      });
  }

  function search(query) {
    return lunrIndex.search(query).map(function(result) {
      return documents.filter(function(page) {
        try {
          // console.log(page)
          return page.href === result.ref;
        } catch (e) {
          console.log('whoops')
        }
      })[0];
    });
  }

  function renderResults(results) {
    if (!results.length) {
      return;
    }

    results.slice(0, 30).forEach(function(result) {
      var $result = $("<li>");

      $result.append($("<a>", {
        href: result.href,
        text: result.title
      }));

      $result.append(" <small><time>" + result.date + "</time></small>");

      $results.append($result);
    });
  }

  function initUI() {
    $results = $("#results");

    $("#search").keyup(function(){
      // empty previous results
      $results.empty();

      // trigger search when at least two chars provided.
      var query = $(this).val();
      if (query.length < 2) {
        return;
      }

      var results = search(query);

      renderResults(results);
    });
  }

  initLunr();

  $(document).ready(function(){
    initUI();
  });
</script>
</div>
```

We see the script is calling to javascript libraries:

- https://blog.wains.be/js/jquery-2.1.3.min.js
- https://blog.wains.be/js/lunr.js

I host them (in my hugo folder under `/static/js/`) so your web browser does not have to visit external URLs.

Now you can build your site (just type `hugo`), and if you visit the search page, you should be granted with a form.

As soon as your start typing in the field, lunr will start querying the content of `index.json` (that has been retrieved by your browser locally) and display the results.

## Bingo

![](https://blog.wains.be/images/lunr-result.png)

## Acknowledments

- Mark McDonnell
- Joseph Earl