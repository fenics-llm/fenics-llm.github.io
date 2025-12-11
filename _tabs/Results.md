---
icon: fas fa-folder-open
order: 3
---

# Results_ALL-FEM repository

<div id="repo-browser">
  Loading repository contents...
</div>

<script>
  (function () {
    var container = document.getElementById("repo-browser");

    function showError(msg) {
      container.textContent = msg;
    }

    function createEntryElement(item, parentPath) {
      var li = document.createElement("li");
      var fullPath = parentPath ? parentPath + "/" + item.name : item.name;

      if (item.type === "dir") {
        var toggle = document.createElement("span");
        toggle.textContent = "📁 " + item.name;
        toggle.style.cursor = "pointer";
        toggle.style.fontWeight = "600";

        var childrenContainer = document.createElement("ul");
        childrenContainer.style.listStyleType = "none";
        childrenContainer.style.marginLeft = "1.2rem";
        childrenContainer.style.display = "none";

        toggle.addEventListener("click", function () {
          if (childrenContainer.dataset.loaded === "true") {
            childrenContainer.style.display =
              childrenContainer.style.display === "none" ? "block" : "none";
            return;
          }

          childrenContainer.textContent = "Loading...";
          fetch("https://api.github.com/repos/fenics-llm/Results_ALL-FEM/contents/" + fullPath)
            .then(function (response) {
              if (!response.ok) {
                throw new Error("HTTP " + response.status);
              }
              return response.json();
            })
            .then(function (contents) {
              childrenContainer.textContent = "";
              contents
                .sort(function (a, b) {
                  if (a.type !== b.type) return a.type === "dir" ? -1 : 1;
                  return a.name.localeCompare(b.name);
                })
                .forEach(function (child) {
                  childrenContainer.appendChild(createEntryElement(child, fullPath));
                });
              childrenContainer.dataset.loaded = "true";
              childrenContainer.style.display = "block";
            })
            .catch(function (err) {
              childrenContainer.textContent = "Error loading folder: " + err.message;
              console.error(err);
            });
        });

        li.appendChild(toggle);
        li.appendChild(childrenContainer);
      } else if (item.type === "file") {
        var link = document.createElement("a");
        link.textContent = "📄 " + item.name;
        link.href = item.html_url;
        link.target = "_blank";
        link.rel = "noopener noreferrer";
        li.appendChild(link);
      } else {
        li.textContent = item.type + ": " + item.name;
      }

      return li;
    }

    function loadRoot() {
      container.textContent = "Loading repository contents...";

      fetch("https://api.github.com/repos/fenics-llm/Results_ALL-FEM/contents")
        .then(function (response) {
          if (!response.ok) {
            throw new Error("HTTP " + response.status);
          }
          return response.json();
        })
        .then(function (data) {
          var list = document.createElement("ul");
          list.style.listStyleType = "none";
          list.style.paddingLeft = "0";

          data
            .sort(function (a, b) {
              if (a.type !== b.type) return a.type === "dir" ? -1 : 1;
              return a.name.localeCompare(b.name);
            })
            .forEach(function (item) {
              list.appendChild(createEntryElement(item, ""));
            });

          container.textContent = "";
          container.appendChild(list);

          var note = document.createElement("p");
          note.style.fontSize = "0.8rem";
          note.style.marginTop = "0.5rem";
          note.textContent =
            "Click folders to expand; files open on GitHub in a new tab.";
          container.appendChild(note);
        })
        .catch(function (err) {
          showError("Failed to load repository contents: " + err.message);
          console.error(err);
        });
    }

    // Run immediately (no need to wait for DOMContentLoaded because the script is after the div)
    loadRoot();
  })();
</script>
