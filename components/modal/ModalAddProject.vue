<template>
  <div class="modal fade in" @close="closeModal" :class="{'isShow': isShow}">
    <div class="modal-dialog">

      <!-- Modal content-->
      <div class="modal-content">
        <div class="modal-header">
          <button type="button" class="close" @click="closeModal">&times;</button>
          <h4 class="modal-title">Add Project</h4>
        </div>
        <div class="modal-body">
          <div class="form-group">
            <input type="text" class="form-control" v-model="newProject" placeholder="New Project">
          </div>
          <div class="form-group">
            <input type="color" name="choise-color" class="form-control" v-model="newProjectColor">
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-primary" @click="addProject">Save</button>
          <button type="button" class="btn btn-default" @click="closeModal">Close</button>
        </div>
      </div>

    </div>
  </div>
</template>

<script>
    import { EventBus } from '~/store/event-bus.js'
    import _ from 'lodash'

    export default {
        name: 'ModalAddProject',
        data() {
            return {
                isShow: false,
                newProject: '',
                newProjectColor: '#000000'
            }
        },
        created() {
            EventBus.$on('openModalAddProject', isShow => {
                this.isShow = isShow
            })
        },
        computed: {
            projects() {
                return this.$store.state.projects
            }
        },
        methods: {
            addProject() {
                if (this.newProject !== '') {
                    // const link = this.newProject.split(" ").join("-").toLowerCase()
                    const link = _.toLower(_.join(_.split(this.newProject, ' '), '-'))
                    const project = {
                        link: link,
                        name: this.newProject,
                        color: this.newProjectColor,
                        number: 0
                    }

                    this.$store.dispatch('addProject', project)

                    localStorage.setItem('projects', JSON.stringify(this.projects))
                }
            },
            closeModal() {
                this.isShow = !this.isShow
                EventBus.$emit('closeModalAddProject', this.isShow)
            }
        }
    }
</script>

<style lang="scss" scoped>
  .modal {
    display: block;
    opacity: 0;
    visibility: hidden;
    z-index: -1;
    /*transition: opacity 0.3s ease-out, visibility 0.3s ease-out;*/
    background-color: rgba(0, 0, 0, 0.5);
    &-dialog {
      position: relative;
      max-width: 700px;
      width: calc(100% - 50px);
      height: 100vh;
      margin: 0 auto;
      display: flex;
      justify-content: center;
      align-items: center;
    }
    &-content {
      width: 100%;
    }
  }
  .modal.isShow {
    opacity: 1;
    visibility: visible;
    z-index: 2;
  }
  .modal.fade.in {
    .modal-dialog {
      opacity: 0;
      transform: translateY(-20%);
      visibility: hidden;
      transition: opacity 0.3s ease-out, transform 0.3s ease-out, visibility 0.3s ease-out;
    }
  }
  .modal.fade.in.isShow {
    .modal-dialog {
      opacity: 1;
      transform: translate(0);
      visibility: visible;
    }
  }
</style>
