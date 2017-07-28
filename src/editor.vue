<template>
  <div :class="classPrefix">
    <div :class="classes.actionbar" v-if="actions.length">
      <button v-for="action in actions" :key="action.title" :title="action.title" @click="action.result" :class="classes.button" v-html="action.icon">
      </button>
    </div>
    <div v-once v-html="content" @keydown="preventTab" @input="onChange" ref="content" contenteditable :class="classes.content"></div>
  </div>
</template>

<script>
export default {
  props: {
    value: {
      type: String,
      default: ''
    },
    settings: {
      type: Object,
      default () {
        return {}
      }
    }
  },
  data () {
    return {
      content: ''
    }
  },
  watch: {
    content (val) {
      this.$emit('input', val)
    },
    value (val) {
      this.content = val
    }
  },
  computed: {
    classPrefix () {
      return this.settings.classPrefix || 'pell'
    },
    classes () {
      return Object.assign({
        actionbar: this.classPrefix + '-actionbar',
        button: this.classPrefix + '-button',
        content: this.classPrefix + '-content'
      }, this.settings.classes)
    },
    actions () {
      const actions = {
        bold: {
          icon: '<b>B</b>',
          title: 'Bold',
          result: () => this.exec('bold')
        },
        italic: {
          icon: '<i>I</i>',
          title: 'Italic',
          result: () => this.exec('italic')
        },
        underline: {
          icon: '<u>U</u>',
          title: 'Underline',
          result: () => this.exec('underline')
        },
        strikethrough: {
          icon: '<strike>S</strike>',
          title: 'Strike-through',
          result: () => this.exec('strikeThrough')
        },
        heading1: {
          icon: '<b>H<sub>1</sub></b>',
          title: 'Heading 1',
          result: () => this.exec('formatBlock', '<H1>')
        },
        heading2: {
          icon: '<b>H<sub>2</sub></b>',
          title: 'Heading 2',
          result: () => this.exec('formatBlock', '<H2>')
        },
        paragraph: {
          icon: '&#182;',
          title: 'Paragraph',
          result: () => this.exec('formatBlock', '<P>')
        },
        quote: {
          icon: '&#8220; &#8221;',
          title: 'Quote',
          result: () => this.exec('formatBlock', '<BLOCKQUOTE>')
        },
        olist: {
          icon: '&#35;',
          title: 'Ordered List',
          result: () => this.exec('insertOrderedList')
        },
        ulist: {
          icon: '&#8226;',
          title: 'Unordered List',
          result: () => this.exec('insertUnorderedList')
        },
        code: {
          icon: '&lt;/&gt;',
          title: 'Code',
          result: () => this.exec('formatBlock', '<PRE>')
        },
        line: {
          icon: '&#8213;',
          title: 'Horizontal Line',
          result: () => this.exec('insertHorizontalRule')
        },
        link: {
          icon: '&#128279;',
          title: 'Link',
          result: () => {
            const url = window.prompt('Enter the link URL')
            if (url) this.exec('createLink', url)
          }
        },
        image: {
          icon: '&#128247;',
          title: 'Image',
          result: () => {
            const url = window.prompt('Enter the image URL')
            if (url) this.exec('insertImage', url)
          }
        }
      }
      return this.settings.actions
        ? this.settings.actions.map(action => {
          if (typeof action === 'string') {
            return actions[action]
          } else if (actions[action.name]) {
            return {
              ...actions[action.name],
              ...action
            }
          }
          return action
        })
        : Object.keys(actions).map(action => actions[action])
    }
  },
  methods: {
    exec (command, value = null) {
      document.execCommand(command, false, value)
    },
    preventTab (event) {
      event.which === 9 && event.preventDefault()
    },
    onChange () {
      this.content = this.$refs.content.innerHTML
    }
  },
  created () {
    this.content = this.value
  },
  setContent (content) {
    this.$refs.content.innerHTML = content
  }
}
</script>


<style lang="scss" scoped>
  
</style>

