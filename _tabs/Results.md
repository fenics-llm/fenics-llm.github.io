---
icon: fas fa-folder-open
order: 7
---

# Results_ALL-FEM repository

<div id="repo-browser">
  Loading repository contents...
</div>

<script>
  // Simple GitHub repo browser for fenics-llm/Results_ALL-FEM

  async function fetchRepoContents(path = "") {
    const apiBase = "https://api.github.com/repos/fenics-llm/Results_ALL-FEM/contents";
    const url = path ? apiBase + "/" + path : apiBase;
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error("GitHub API error: " + response.status);
    }
    return response.json();
  }

  function createEntryElement(item, parentPath) {
    const li = document.createElement("li");
    const fullPath = parentPath ? parentPath + "/" + item.name : item.name;

    if (item.type === "dir") {
      const toggle = document.createElement("span");
      toggle.textContent = "📁 " + item.name;
      toggle.style.cursor = "pointer";
      toggle.style.fontWeight = "600";

      const childrenContainer = document.createElement("ul");
      childrenContainer.style.listStyleType = "none";
      childrenContainer.style.marginLeft = "1.2rem";
      childrenContainer.style.display = "none"; // collapsed by default

      toggle.addEventListener("click", async () => {
        if (childrenContainer.dataset.loaded === "true") {
          // just toggle visibility
          childrenContainer.style.display =
            childrenContainer.style.display === "none" ? "block" : "none";
          return;
        }
        // first time: load contents
        childrenContainer.textContent = "Loading...";
        try {
          const contents = await fetchRepoContents(fullPath);
          childrenContainer.textContent = "";
          contents
            .sort((a, b) => {
              // folders first, then files, alphabetically
              if (a.type !== b.type) return a.type === "dir" ? -1 : 1;
              return a.name.localeCompare(b.name);
            })
            .forEach(child => {
              childrenContainer.appendChild(createEntryElement(child, fullPath));
            });
          childrenContainer.dataset.loaded = "true";
          childrenContainer.style.display = "block";
        } catch (err) {
          childrenContainer.textContent = "Error loading folder.";
          console.error(err);
        }
      });

      li.appendChild(toggle);
      li.appendChild(childrenContainer);
    } else if (item.type === "file") {
      const link = document.createElement("a");
      link.textContent = "📄 " + item.name;
      link.href = item.html_url; // GitHub HTML view
      link.target = "_blank";
      link.rel = "noopener noreferrer";
      li.appendChild(link);
    } else {
      // other types (symlink, submodule, etc.)
      li.textContent = item.type + ": " + item.name;
    }

    return li;
  }

  async function renderRepoRoot() {
    const container = document.getElementById("repo-browser");
    container.textContent = "Loading repository contents...";

    try {
      const data = await fetchRepoContents("");

      const list = document.createElement("ul");
      list.style.listStyleType = "none";
      list.style.paddingLeft = "0";

      data
        .sort((a, b) => {
          if (a.type !== b.type) return a.type === "dir" ? -1 : 1;
          return a.name.localeCompare(b.name);
        })
        .forEach(item => {
          list.appendChild(createEntryElement(item, ""));
        });

      container.textContent = "";
      container.appendChild(list);
      const note = document.createElement("p");
      note.style.fontSize = "0.8rem";
      note.style.marginTop = "0.5rem";
      note.textContent = "Click folders to expand; files open on GitHub in a new tab.";
      container.appendChild(note);
    } catch (err) {
      container.textContent = "Failed to load repository contents.";
      console.error(err);
    }
  }

  document.addEventListener("DOMContentLoaded", renderRepoRoot);
</script>
