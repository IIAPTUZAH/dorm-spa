<template>
    <!-- <div>
      <div class="add-row">
        <el-row>
          <el-button @click="addStudent(scope.$index, scope.row)" icon="el-icon-plus" size="mini" >Добавить студента</el-button>
        </el-row>
      </div> -->
  <div class='page-component__scroll'>
  <div class='el-scrollbar__wrap'>
    <el-backtop target=".page-component__scroll .el-scrollbar__wrap"></el-backtop>  <!--Скрипт добавляющий кнопку На верх -->
    
    <div class="my-table">
      <el-container style="height: 50px">
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
            :index="indexMethod"
            width="50">
          </el-table-column>
          <el-table-column
            prop="fio"
            label="ФИО"
            width="370">
              <template slot-scope="scope">
                <input style="width: 300px; height: 20px;" v-model.lazy="scope.row.fio" :readonly="scope.row.edited">
              </template>
          </el-table-column>      
          <el-table-column
            prop="birthdate"
            label="Дата рождения"
            width="150">
              <template slot-scope="scope">
                <input style="width: 100px; height: 20px;" v-model="scope.row.birthdate" :readonly="scope.row.edited">
              </template>        
          </el-table-column>
          <el-table-column
            prop="room"
            label="Комната"
            sortable
            width="100">
            <template slot-scope="scope">
              <input style="width: 60px; height: 20px; text-align: center;" v-model="scope.row.room" :readonly="scope.row.edited">
            </template>   
          </el-table-column>
          <el-table-column
            prop="university"
            label="ВУЗ"
            width="100">
            <template slot-scope="scope">
              <input style="width: 60px; height: 20px;" v-model="scope.row.university" :readonly="scope.row.edited">
            </template>          
          </el-table-column>
          <el-table-column
            prop="group"
            label="Группа"
            width="140">
            <template slot-scope="scope">
              <input style="width: 90px; height: 20px;" v-model="scope.row.group" :readonly="scope.row.edited">
            </template>          
          </el-table-column>  
          <el-table-column
            prop="education"
            label="Форма обучения"
            width="70">
            <template slot-scope="scope">
              <input style="width: 30px; height: 20px;" v-model="scope.row.education" :readonly="scope.row.edited">
            </template>          
          </el-table-column>
          <el-table-column
            label="Действия"
            width="270">
            <template slot-scope="scope">
              <el-button-group>
                <el-button @click="handleEdit(scope.$index, scope.row)" icon="el-icon-edit" size="mini" v-if="scope.row.edited">Редактировать</el-button>
                <el-button @click="handleSave(scope.$index, scope.row)" type="text" icon="el-icon-check" size="medium" v-else-if="!scope.row.edited">Сохранить</el-button>
             
                <el-button @click="addStudent(scope.$index, scope.row)" icon="el-icon-plus" size="mini" >Добавить</el-button>             
                <el-button @click="deleteStudent(scope.$index, scope.row)" type="danger" icon="el-icon-delete" size="mini" circle></el-button>
              
              </el-button-group>
            </template>
          </el-table-column>  
      </el-table>

      <div class="block"> <!-- Блок с пагинацией -->
          <el-pagination
            background
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page.sync="currentPage"
            :page-sizes="[10, 20, 30, 200]"
            :page-size="10"
            layout="sizes, prev, pager, next"
            :total="200">
          </el-pagination>
      </div>
    </div>
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
        },
        perPage: 10,
        currentPage: 1,
        total: [],
      }
    },
    created() {
      this.getAllitems();
    },
    methods: {
      getAllitems() {
        axios.get(this.endpoint + '?perPage=10&page=0')   // По умолчанию выводим 10 студентов.
          .then(response => {
            this.items = response.data;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          })
      },
      handleSizeChange(val) {       // Определяем количество отображаемых элементов
        this.perPage = val,
        axios.get(this.endpoint + '?perPage=' + this.perPage + '&page=' + (this.currentPage * this.perPage - this.perPage))
          .then(response => {
            this.items = response.data;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          }),
        console.log(`${val} items per page`);
      },
      handleCurrentChange(val) {      // Переходим на выбранную страницу
        this.currentPage = val,
        axios.get(this.endpoint + '?perPage=' + this.perPage + '&page=' + (this.currentPage * this.perPage - this.perPage))
          .then(response => {
            this.items = response.data;
          })
          .catch(error => {
            console.log('-----error-------');
            console.log(error);
          }),
        console.log(`current page: ${val}`);
      },
      handleSave(index, row) {
        axios.put('http://127.0.0.1:5000/user/' + this.items[index].studentID, {
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
        axios.delete('http://127.0.0.1:5000/user/' + this.items[index].studentID)   // Удаляем по индексу из БД
        .then(response => {
                  console.log(response);
               }),
        row.splice(index, 1);   // При удалении убираем одну запись
      },
      deleteStudent(index, row) {
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
        axios.post(this.endpoint, {
          fio: row.fio,
          university: row.university,
          birthdate: row.birthdate,
          room: row.room,
          group: row.group,
          education: row.education,
          male: row.male,
          studentID: this.items[index].studentID    // Надо добавить скрытые поля для новых
        })
        .then(function (response) {
                    console.log(response);
                })
        .catch(function (error) {
                    console.log(error);
                });
      },
      indexMethod(index) {
        return index + (this.currentPage * this.perPage - this.perPage) + 1;  // Считаем номер студента
      }
    }
  }
</script>

<style lang="scss">
@import url("//unpkg.com/element-ui@2.13.0/lib/theme-chalk/index.css");

  .el-header, .el-footer {
    background-color: #E9EEF3;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .page-component__scroll {
      height: 900px;
      margin-top: 40px;
  }
  
  .page-component__scroll>.el-scrollbar__wrap {
      overflow-x: auto;
  }

  .el-scrollbar__wrap {
      overflow: scroll;
      height: 100%;
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
