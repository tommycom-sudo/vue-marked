<template>
    <el-container style="border: 1px solid #eee">
      <el-aside width="200px" style=" text-align: left;background-color: rgb(238, 241, 246)">
        <el-button :title="titles" @click="addNote" type="primary" icon="el-icon-circle-plus-outline">新增</el-button>  
        <el-menu  v-bind:default-active="selectedId">
          <el-menu-item @click="selectNote(note)" 
            v-bind:index="note.id"  v-bind:key='note.id' v-for='note of sortedNotes'>
            <div style="vertical-align: middle;">
            <span slot="title">{{ note.title }}</span>
            <i class="el-icon-star-on icon" v-if="note.favorite"></i>
            </div>
          </el-menu-item>
       </el-menu>
      </el-aside>
      <template v-if="selectedNote">
      <el-main style="padding: 0px; width: 50%; background-color: #dddddd;">
        <el-container>
          <el-header style="height:50px;padding: 5px 5px 5px 5px">
            <el-row>
              <el-col :span="12">
                <el-input size='small' style="text-align: left" v-model="selectedNote.title"/>
              </el-col>
              <el-col :span="12" style="padding-left: 5px;text-align: left">
                <el-button-group>
                  <el-button @click="favoriteNote" size='small'  title="收藏" type="primary" icon="el-icon-star-on"></el-button>
                  <el-button @click="removeNote" size='small' title="删除当前note" type="primary" icon="el-icon-delete-solid"></el-button>
                
                </el-button-group>
              </el-col>
            </el-row>
          </el-header>
          <el-main style="padding: 0 5px 10px 0;overflow:hidden;height:10%;width: 100%">
              <textarea style="margin: 0;height:100%; width: 99%" 
                v-model="selectedNote.content"></textarea>
          </el-main>
          <el-footer style="height: 35px;padding: 0 5px 10px 0;" >
            <el-row>
              <el-col :span="8">
                <span class="label">Created:</span>
                <span>{{ selectedNote.created | date}}</span>
              
              </el-col>
              <el-col :span="16" style="padding-left: 5px;text-align: left">
                <el-button-group>
                  <el-button @click="favoriteNote" size='small'  title="收藏" type="primary" icon="el-icon-star-on"></el-button>
                  <el-button @click="removeNote" size='small' title="删除当前note" type="primary" icon="el-icon-delete-solid"></el-button>
                
                </el-button-group>
              </el-col>
            </el-row>
          </el-footer>
        </el-container>

      </el-main>
      
      <el-main style="margin-left: 2px;padding: 0;width: 50%;text-align: left;background-color: #eeeeee;" v-html="calc"></el-main>
      </template>
    </el-container>
  
</template>
<style lang="stylus">
.label {
 
  color : #eeeeee;
 
}
.icon {
  float: right;
  padding-top: 20px;
}
.el-menu-item.is-active
  background-color: #DDDEFF
body,
html
  height: 100%
  margin: 0px
.el-container
  height: 100%
.el-footer
  background-color: #B3C0D1;
  color: #333;
  text-align: left;
  height: 30px;
  line-height: 30px;
  margin: 0 5px 0px 0;
</style>
<script>
import marked from 'marked';

export default {
  name: 'HelloWorld',
  data () {
    return {
      notes: JSON.parse(localStorage.getItem('notes')) || [],
      selectedId: localStorage.getItem('selected-id') || null
    }
  },
  watch: {
    selectedId(val){
      localStorage.setItem('selected-id', val)
    },
    notes: {
      handler: 'saveNotes',
      deep: true
    }
  },
  methods: {
    favoriteNote(){
      this.selectedNote.favorite = ! this.selectedNote.favorite
    },
    removeNote(){
      if (this.selectedNote && confirm('Delete the note?')) {
        const index = this.notes.indexOf(this.selectedNote)
        if (index !== -1) {
          this.notes.splice(index, 1)
        }
      }
    },
    selectNote (note) {
      // console.log(arguments[0], arguments[1])
      this.selectedId = note.id
    },
    addNote () {
      const time = Date.now()
      const note = {
        id: String(time),
        title: '新note ' + (this.notes.length + 1),
        content: '**hi** this notebook is using [markdown](https://www.baidu.comm)',
        created: time,
        favorite: false
      }
      this.notes.push(note)
      this.selectedId = note.id
    },
    saveNotes(){
      localStorage.setItem('notes', JSON.stringify(this.notes))
      console.log('Notes saved!', new Date())
    }
  },
  props: {
    msg: String
  },
  computed: {
    sortedNotes(){
      return this.notes.slice()
        .sort((a,b) => a.created - b.created)
        .sort((a,b) => (a.favorite === b.favorite) ? 0 
          : a.favorite ? -1 
          : 1)
    },
    selectedNote(){
      //返回与selectedID 匹配的笔记 
      return this.notes.find(note => note.id === this.selectedId)
    },
    titles () {
      return '共有:' + this.notes.length + '条note'
    },
    calc () {
      return this.selectedNote ?  marked(this.selectedNote.content) : ''
    }
  }
}
</script>
