<template>
  <main>
    <div class="header">
      <h1>
        Convert from
        <a href="https://gethomepage.dev/" target="_blank" rel="noopener noreferrer">Homepage</a> to
        <a href="https://github.com/glanceapp/glance" target="_blank" rel="noopener noreferrer">Glance</a>
      </h1>
      <p>A very simple site by <a href="https://jackbailey.dev">Jack Bailey</a></p>
    </div>
    <div class="editors">
      <div class="editor-input">
        <h2>Input - Homepage services.yaml</h2>
        <MonacoEditor
          theme="vs"
          v-model:value="code"
          language="yaml"
          :options="options"
        ></MonacoEditor>
      </div>
      <div class="editor-output">
        <h2>Output - glance.yml</h2>
        <MonacoEditor
          theme="vs"
          v-model:value="output"
          language="yaml"
          :options="options"
        ></MonacoEditor>
      </div>
    </div>
  </main>
</template>

<script>
import MonacoEditor from 'monaco-editor-vue3';
import YAML from "yaml";
export default {
  components: {
    MonacoEditor
  },
  computed: {
    output() {
      let outputObj = {
          type: "bookmarks",
          groups: []
      };
      let yamlData = YAML.parse(this.code);

      console.log(yamlData);

      for (let group in yamlData) {
          let [groupName, groupData] = Object.entries(yamlData[group])[0];

          // output.groups.push({

          // });

          let groupObj = {
              title: groupName,
              links: []
          };

          for (let link of groupData) {
              let [linkName, linkData] = Object.entries(link)[0];

              if (!linkData.href) {
                  continue;
              }

              groupObj.links.push({
                  title: linkName,
                  url: linkData.href,
                  icon: linkData.icon ? linkData.icon.includes("/") ? linkData.icon : `https://cdn.jsdelivr.net/gh/walkxcode/dashboard-icons/svg/${linkData.icon}.svg` : false
              });
          }

          if (groupObj.links.length === 0) {
              continue;
          }

          outputObj.groups.push(groupObj);
      }

      return YAML.stringify(outputObj);
    }
  },
  data() {
    return {
      code: `- Server:
    - Unraid: 
        icon: unraid
        href: https://unraid.example.com
    
    - Nextcloud:
        icon: nextcloud
        href: https://cloud.example.com

- Other:
    - Adguard Home:
        icon:  adguard-home
        href: https://dns.example.com    

    - Authentik:
        icon: authentik
        href: https://auth.example.com

    - Scrutiny:
        icon: scrutiny
        href: https://scrutiny.example.com
    
    - Gotify:
        icon: gotify
        href: https://gotify.example.com

    - Mealie:
        icon: mealie
        href: https://mealie.example.com

    - Paperless:
        icon: paperless-ngx
        href: https://docs.example.com`,
      options: {
        scrollBeyondLastLine: false,
        minimap: { enabled: false },
        scrollbar: {
          vertical:"hidden",
        },
        overviewRulerLanes: 0,
      }
    }
  }
}
</script>

<style lang="scss">
html, body {
  height: 100%;
  width: 100%;
  padding: 0;
  margin: 0;
}

#app {
  height: 100%;
  width: 100%;
  background-color: var(--background-2);
  font-family: "Quicksand", Arial, Helvetica, sans-serif;
  display: grid;
  place-content: center;
  main {
    width: 80vw;
    height: 80vh;
    background-color: var(--background-1);
    color: var(--foreground-1);
    padding: 2rem;
    border-radius: 2rem;
    box-sizing: border-box;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    display: flex;
    flex-direction: column;
    .header {
      h1 {
        text-align: center;
        a {
          color: var(--accent);
        }
      }

      p {
        text-align: center;
        a {
          color: var(--accent);
        }
      }
    }

    .editors {
      display: grid;
      grid-template-columns: 1fr 1fr;
      flex-grow: 1;
      height: 100%;

      .editor-input,
      .editor-output {
        display: flex;
        flex-direction: column;
        height: 100%;
        width: 100%;

        h2 {
          font-weight: 400;
          font-size: 1rem;
          text-align: center;
        }

        .monaco-editor-vue3 {
          width: 100%;
          height: 100%;
          :deep(.monaco-editor) {
            height: 100%;
          }
        }
      }
    }
  }
  @media screen and (max-width: 1200px){
    main {
      height: 99vh;
      width: 99vw;
      .header {
        h1 {
          text-align: center;
        }
      }
      .editors {
        grid-template-columns: 1fr;
        grid-template-rows: 1fr 1fr;
      }
    }
  }
}
</style>