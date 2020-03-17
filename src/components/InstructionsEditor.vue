<template>
  <div :class="{ 'editor': true, 'readonly': !editable }">
    <editor-menu-bar v-if="editable" :editor="editor" v-slot="{ commands, isActive }">
      <div class="menubar">
        <div class="bar-group">
          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.bold() }"
            @click="commands.bold"
          >
            <v-icon>mdi-format-bold</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.italic() }"
            @click="commands.italic"
            >
            <v-icon>mdi-format-italic</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.strike() }"
            @click="commands.strike"
            >
            <v-icon>mdi-format-strikethrough</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.underline() }"
            @click="commands.underline"
            >
            <v-icon>mdi-format-underline</v-icon>
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.code() }"
            @click="commands.code"
          >
            <v-icon>mdi-code-tags</v-icon>
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.paragraph() }"
            @click="commands.paragraph"
            >
            <v-icon>mdi-format-paragraph</v-icon>
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button header-btn"
            :class="{ 'is-active': isActive.heading({ level: 1 }) }"
            @click="commands.heading({ level: 1 })"
          >
            H1
          </button>

          <button
            class="menubar__button header-btn"
            :class="{ 'is-active': isActive.heading({ level: 2 }) }"
            @click="commands.heading({ level: 2 })"
          >
            H2
          </button>

          <button
            class="menubar__button header-btn"
            :class="{ 'is-active': isActive.heading({ level: 3 }) }"
            @click="commands.heading({ level: 3 })"
          >
            H3
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.bullet_list() }"
            @click="commands.bullet_list"
          >
            <v-icon>mdi-format-list-bulleted</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.ordered_list() }"
            @click="commands.ordered_list"
          >
            <v-icon>mdi-format-list-numbered</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.blockquote() }"
            @click="commands.blockquote"
          >
            <v-icon>mdi-format-quote-close</v-icon>
          </button>

          <button
            class="menubar__button"
            :class="{ 'is-active': isActive.code_block() }"
            @click="commands.code_block"
          >
            <v-icon>mdi-code-tags</v-icon>
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button"
            @click="commands.horizontal_rule"
          >
            <v-icon>mdi-minus</v-icon>
          </button>
        </div>

        <div class="bar-group">
          <button
            class="menubar__button"
            @click="commands.undo"
          >
            <v-icon>mdi-undo</v-icon>
          </button>

          <button
            class="menubar__button"
            @click="commands.redo"
          >
            <v-icon>mdi-redo</v-icon>
          </button>
        </div>

      </div>
    </editor-menu-bar>

    <editor-content class="editor__content" :editor="editor" />
  </div>
</template>

<script>
import { Editor, EditorContent, EditorMenuBar } from 'tiptap'
import {
  Blockquote,
  CodeBlock,
  HardBreak,
  Heading,
  HorizontalRule,
  OrderedList,
  BulletList,
  ListItem,
  TodoItem,
  TodoList,
  Bold,
  Code,
  Italic,
  Link,
  Strike,
  Underline,
  History
} from 'tiptap-extensions'

export default {
  props: {
    content: String,
    editable: {
      type: Boolean,
      required: false,
      default: true
    }
  },
  components: {
    EditorContent,
    EditorMenuBar
  },
  data: () => ({
    editor: null
  }),
  watch: {
    content (newVal) {
      if (this.editor) {
        this.editor.setContent(newVal || '')
      }
    },
    editable (newVal) {
      if (this.editor) {
        this.editor.setOptions({
          editable: newVal
        })
      }
    }
  },
  mounted () {
    this.editor = new Editor({
      extensions: [
        new Blockquote(),
        new BulletList(),
        new CodeBlock(),
        new HardBreak(),
        new Heading({ levels: [1, 2, 3] }),
        new HorizontalRule(),
        new ListItem(),
        new OrderedList(),
        new TodoItem(),
        new TodoList(),
        new Link(),
        new Bold(),
        new Code(),
        new Italic(),
        new Strike(),
        new Underline(),
        new History()
      ],
      content: this.content,
      editable: this.editable,
      onUpdate: state => this.$emit('change', state.getHTML())
    })
  },
  beforeDestroy () {
    this.editor.destroy()
  }
}
</script>

<style lang="scss" scoped>
$color-black: #000000;
$color-white: #ffffff;
$color-grey: #dddddd;
.editor {
  position: relative;
  // max-width: 60rem;
  margin: 0 auto 1rem auto;
  &:not(.readonly) {
    border: 1px solid rgba(255, 255, 255, 0.1);
    border-radius: 5px;
  }
  // padding: 1em;

  &__content {
    margin-left: 0.7em;
    margin-right: 0.7em;
    font-size: 1.1rem;
    overflow-wrap: break-word;
    word-wrap: break-word;
    word-break: break-word;

    * {
      caret-color: currentColor;
    }

    pre {
      padding: 0.7rem 1rem;
      border-radius: 5px;
      background: $color-black;
      color: $color-white;
      font-size: 0.8rem;
      overflow-x: auto;

      code {
        display: block;
      }
    }

    p code {
      display: inline-block;
      padding: 0 0.4rem;
      border-radius: 5px;
      font-size: 0.8rem;
      font-weight: bold;
      background: rgba($color-black, 0.1);
      color: rgba($color-black, 0.8);
    }

    ul,
    ol {
      padding-left: 1rem;
    }

    li > p,
    li > ol,
    li > ul {
      margin: 0;
    }

    a {
      color: inherit;
    }

    blockquote {
      border-left: 3px solid rgba($color-black, 0.1);
      color: rgba($color-black, 0.8);
      padding-left: 0.8rem;
      font-style: italic;

      p {
        margin: 0;
      }
    }

    img {
      max-width: 100%;
      border-radius: 3px;
    }

    table {
      border-collapse: collapse;
      table-layout: fixed;
      width: 100%;
      margin: 0;
      overflow: hidden;

      td, th {
        min-width: 1em;
        border: 2px solid $color-grey;
        padding: 3px 5px;
        vertical-align: top;
        box-sizing: border-box;
        position: relative;
        > * {
          margin-bottom: 0;
        }
      }

      th {
        font-weight: bold;
        text-align: left;
      }

      .selectedCell:after {
        z-index: 2;
        position: absolute;
        content: "";
        left: 0; right: 0; top: 0; bottom: 0;
        background: rgba(200, 200, 255, 0.4);
        pointer-events: none;
      }

      .column-resize-handle {
        position: absolute;
        right: -2px; top: 0; bottom: 0;
        width: 4px;
        z-index: 20;
        background-color: #adf;
        pointer-events: none;
      }
    }

    .tableWrapper {
      margin: 1em 0;
      overflow-x: auto;
    }

    .resize-cursor {
      cursor: ew-resize;
      cursor: col-resize;
    }

  }
}

.menubar {
  background-color: rgba(255, 255, 255, 0.1);
  border-radius: 5px;
  padding: 0.8em;
  margin-bottom: 1rem;
  transition: visibility 0.2s 0.4s, opacity 0.2s 0.4s;

  &.is-hidden {
    visibility: hidden;
    opacity: 0;
  }

  &.is-focused {
    visibility: visible;
    opacity: 1;
    transition: visibility 0.2s, opacity 0.2s;
  }

  &__button {
    font-weight: bold;
    display: inline-flex;
    background: transparent;
    border: 0;
    color: $color-white;
    padding: 0.2rem 0.5rem;
    border-radius: 3px;
    cursor: pointer;
    // height: 24px;
    // width: 24px;

    &:hover {
      background-color: rgba($color-black, 0.05);
    }

    &.is-active {
      background-color: rgba($color-black, 0.1);
    }
    &.header-btn {
      font-size: 1.1rem;
      line-height: 25px;
    }
  }

  .bar-group {
    vertical-align: top;
    margin-right: 1em;
    display: inline-block;
    border: 1px solid rgba(255, 255, 255, 0.66);
    border-radius: 4px;
    background: rgba(255, 255, 255, 0.2);
    .menubar__button:not(:last-of-type) {
      border-right: 1px solid white;
    }
  }

  span#{&}__button {
    font-size: 13.3333px;
  }
}
</style>
