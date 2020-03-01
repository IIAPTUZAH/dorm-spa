<template>
    <!-- <div>
      <div class="add-row">
        <el-row>
          <el-button @click="addStudent(scope.$index, scope.row)" icon="el-icon-plus" size="mini" >Добавить студента</el-button>
        </el-row>
      </div> -->
    <div class="my-table">
      <el-container>
        <el-header>СПИСОК СТУДЕНТОВ ПРОЖИВАЮЩИХ В СТУДЕНЧЕСКОМ ОБЩЕЖИТИИ № 3 (ул. Абразивная, д. 48) на 01.10.2019 г.</el-header>
      </el-container>
      <el-table
        :data="items"
        :default-sort = "{prop: 'room', order: 'ascenting'}"
        border
        lazy
        style="width: 100%">

        <el-table-column
          type="index"
          width="50">
        </el-table-column>
        <el-table-column
          prop="fio"
          label="ФИО"
          width="370">
            <template slot-scope="scope">
              <input style="width: 300px; height: 20px;" v-model.lazy="scope.row.fio" :readonly="scope.row.edited"></input>
            </template>
        </el-table-column>      
        <el-table-column
          prop="birthdate"
          label="Дата рождения"
          width="150">
            <template slot-scope="scope">
              <input style="width: 70px; height: 20px;" v-model="scope.row.birthdate" :readonly="scope.row.edited"></input>
            </template>        
        </el-table-column>
        <el-table-column
          prop="room"
          label="Комната"
          sortable
          width="100">
          <template slot-scope="scope">
            <input style="width: 60px; height: 20px; text-align: center;" v-model="scope.row.room" :readonly="scope.row.edited"></input>
          </template>   
        </el-table-column>
        <el-table-column
          prop="university"
          label="ВУЗ"
          width="100">
          <template slot-scope="scope">
            <input style="width: 70px; height: 20px;" v-model="scope.row.university" :readonly="scope.row.edited"></input>
          </template>          
        </el-table-column>
        <el-table-column
          prop="group"
          label="Группа"
          width="140">
          <template slot-scope="scope">
            <input style="width: 90px; height: 20px;" v-model="scope.row.group" :readonly="scope.row.edited"></input>
          </template>          
        </el-table-column>  
        <el-table-column
          prop="education"
          label="Форма обучения"
          width="70">
          <template slot-scope="scope">
            <input style="width: 50px; height: 20px; text-align: center;" v-model="scope.row.education" :readonly="scope.row.edited"></input>
          </template>          
        </el-table-column>
        <el-table-column
          label="Действия"
          width="270">
          <template slot-scope="scope">
            <el-button @click="handleEdit(scope.$index, scope.row)" icon="el-icon-edit" size="mini" v-if="scope.row.edited">Редактировать</el-button>
            <el-button @click="handleSave(scope.$index, scope.row)" type="text" icon="el-icon-check" size="medium" v-else-if="!scope.row.edited">Сохранить</el-button>
            <el-row>
            <el-button @click="addStudent(scope.$index, scope.row)" icon="el-icon-plus" size="mini" >Добавить</el-button>             
            <el-button @click="open(scope.$index, scope.row)" type="primary" icon="el-icon-delete" size="mini" circle></el-button>
            </el-row>
          </template>
      </el-table-column>  
      </el-table>
    </div>
  </div>
</template>


<script>
  import axios from 'axios'
  export default {
    data () {
      return {
        items: [],
        endpoint: 'http://127.0.0.1:5000/users',
        form: {
          fio: '',
          university: '',
          birthdate: '',
          room: '',
          group: '',
          education: '',
          male: ''
        }
      }
    },
    created() {
      this.getAllitems();
    },
    methods: {
      getAllitems() {
        axios.get(this.endpoint)
          .then(response => {
            this.items = response.data;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          })
      },
      handleSave(index, row) {
        axios.post(this.endpoint, {
          fio: row.fio,
          university: row.university,
          birthdate: row.birthdate,
          room: row.room,
          group: row.group,
          education: row.education,
          male: row.male
        })
        .then(function (response) {
                    console.log(response);
                })
        .catch(function (error) {
                    console.log(error);
                });
        this.items[index].edited = true;        
      },
           handleEdit(index, row) {
        this.items[index].edited = false;
      },
      deleteRow(index, row) {
        axios.delete('http://127.0.0.1:5000/user/' + this.items[index].studentID)
        .then(response => {
                  console.log(response);
               }),
        row.splice(index, 1);
      },
      open(index, row) {
        this.$confirm('Точно хочешь убрать ' + this.items[index].fio +'? Хорошо подумала, проверила?', 'ВНИМАНИЕ!!!', {
          distinguishCancelAndClose: true,
          confirmButtonText: 'Пшел вон',
          cancelButtonText: 'Ладно, пусть живет',
          beforeClose: (action, instance, done) => {
                        if (action === 'confirm') {
                            instance.confirmButtonLoading = true;
                            instance.confirmButtonText = 'Вот и все, канул в лету';
                            setTimeout(() => {
                                done();
                                setTimeout(() => {
                                    instance.confirmButtonLoading = false;
                                }, 300);
                            }, 800);
                            this.deleteRow(index, this.items);
                        } else {
                            done();
                        }
          }          
        })
          .then(() => {
            this.$message({
              type: 'success',
              message: 'Вот и все, канул в лету'
            });
          })
          .catch(action => {
            this.$message({
              type: 'info',
              message: action === 'Он был на грани выселения'
                ? 'Changes discarded. Proceeding to a new route.'
                : 'Изменения отменены'
            })
          });
      },
      addStudent(index, row){
      this.items.splice(index, 0, {});
      },
    }
  }
</script>

<style lang="scss">
@import url("//unpkg.com/element-ui@2.12.0/lib/theme-chalk/index.css");

  .el-header, .el-footer {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 60px;
  }
</style>



<div class="addRowBtn" v-on:click.prevent="addRow" value=""><i class="fa fa-plus" aria-hidden="true"></i></div>
</div>
methods: {
            addRow: function (event) {
              lastId =  this.labels.length;
              var newRow={
                id: this.nextBarId++, 
                percentual: this.value, 
                label: this.label , 
                icon: this.icon
              };
              this.labels.push( newRow );
            }

readonly(?)
