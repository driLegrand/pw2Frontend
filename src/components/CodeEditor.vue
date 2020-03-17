<template>
  <div :id="'editor-' + name"
    class="exercise-editor-ace-editor"
    :style='{ height: editorHeight }'>
  </div>
</template>

<script>
import ace from 'ace-builds/src-noconflict/ace'
import 'ace-builds/src-noconflict/theme-monokai'
import 'ace-builds/src-noconflict/mode-python'
import 'ace-builds/src-noconflict/mode-javascript'
import 'ace-builds/src-noconflict/mode-c_cpp'
import 'ace-builds/webpack-resolver'

export default {
  props: {
    lang: String,
    content: String,
    name: String,
    editorHeight: {
      type: String,
      default: '20rem'
    }
  },
  data: () => ({
    editor: null,
    contentJustChanged: false
  }),
  watch: {
    editorHeight () {
      this.editor.resize()
    },
    content (newVal) {
      if (this.editor && newVal !== this.editor.getValue()) {
        this.contentJustChanged = true
        this.editor.setValue(newVal)
        this.editor.clearSelection()
      }
    },
    lang (newVal) {
      if (this.editor) {
        this.changeLang(newVal)
      }
    }
  },
  mounted () {
    this.editor = ace.edit('editor-' + this.name)
    this.editor.setTheme('ace/theme/monokai')
    this.editor.setFontSize('1.1rem')
    this.changeLang(this.lang)
    this.editor.session.on('change', (e) => {
      // avoid recoil when using setContent(newVal)
      if (!this.contentJustChanged) {
        this.onChange(e)
      } else {
        this.contentJustChanged = false
      }
    })
    if (this.content) {
      this.editor.setValue(this.content)
      this.editor.clearSelection()
    }
  },
  beforeDestroy () {
    if (this.editor) {
      this.editor.destroy()
      this.editor.container.remove()
    }
  },
  methods: {
    changeLang (lang) {
      if (typeof lang === 'undefined') {
        lang = 'python'
      } else if (lang === 'c') {
        lang = 'c_cpp'
      }
      this.editor.session.setMode(`ace/mode/${lang}`)
    },
    onChange (event, force = false) {
      const text = this.editor.getValue()
      if (!force && text === this.content) return
      this.$emit('change', { text })
    }
  }
}
</script>

<style lang="css">
.exercise-editor-ace-editor {
  position: relative;
}

.readonly-highlight {
  background-color: green;
  opacity: 0.2;
  position: absolute;
}
.hide-in-template-highlight {
  background-color: #2196f3;
  opacity: 0.2;
  position: absolute;
}
.ace_gutter-cell.readonly-breakpoint {
  background-color: green;
  opacity: 0.4;
  color: black;
}
.ace_gutter-cell.hide-in-template-breakpoint {
  background-color: #2196f3;
  opacity: 0.4;
  color: black;
}
</style>
