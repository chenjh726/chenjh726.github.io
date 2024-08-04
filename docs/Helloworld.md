# 输出"Hello,world!"
输出"Hello,world!"

```cpp
#include<iostream>
using namespace std;
int main(){
    cout<<"Hello,world!\n";    
    return 0;
}
```

{% if page.meta.comments %}
  <h2 id="__comments">{{ lang.t("meta.comments") }}</h2>
  <script src="https://giscus.app/client.js"
        data-repo="chenjh726/chenjh726.github.io"
        data-repo-id="R_kgDOMeZntw"
        data-category="General"
        data-category-id="DIC_kwDOMeZnt84ChYwb"
        data-mapping="pathname"
        data-strict="0"
        data-reactions-enabled="1"
        data-emit-metadata="0"
        data-input-position="bottom"
        data-theme="light_high_contrast"
        data-lang="zh-CN"
        crossorigin="anonymous"
        async>
  </script>

  <!-- Synchronize Giscus theme with palette -->
  <script>
    var giscus = document.querySelector("script[src*=giscus]")

    // Set palette on initial load
    var palette = __md_get("__palette")
    if (palette && typeof palette.color === "object") {
      var theme = palette.color.scheme === "slate"
        ? "transparent_dark"
        : "light"

      // Instruct Giscus to set theme
      giscus.setAttribute("data-theme", theme) 
    }

    // Register event handlers after documented loaded
    document.addEventListener("DOMContentLoaded", function() {
      var ref = document.querySelector("[data-md-component=palette]")
      ref.addEventListener("change", function() {
        var palette = __md_get("__palette")
        if (palette && typeof palette.color === "object") {
          var theme = palette.color.scheme === "slate"
            ? "transparent_dark"
            : "light"

          // Instruct Giscus to change theme
          var frame = document.querySelector(".giscus-frame")
          frame.contentWindow.postMessage(
            { giscus: { setConfig: { theme } } },
            "https://giscus.app"
          )
        }
      })
    })
  </script>
{% endif %}