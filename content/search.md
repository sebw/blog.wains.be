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
    // Find the item in our index corresponding to the lunr one to have more info
    // Lunr result: 
    //  {ref: "/section/page1", score: 0.2725657778206127}
    // Our result:
    //  {title:"Page1", href:"/section/page1", ...}

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

    // show first ten results
    results.slice(0, 10).forEach(function(result) {
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
