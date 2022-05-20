<template>
  <!-- <div> -->
  <div class="container-fluid" style="background: #FFFFFF;">
    <div class="grid-buttons">
      <button type="button" @click="year = 2022;" class="btn btn-outline-info btn-left btn-w btn-pd fw" :class="year == 2022 ? 'active' : ''">2022</button>
      <button type="button" @click="year = 2023;" class="btn btn-outline-info btn-center btn-w btn-pd fw" :class="year == 2023 ? 'active' : ''">2023</button>
      <button type="button" @click="year = 2024;" class="btn btn-outline-info btn-right btn-w btn-pd fw" :class="year == 2024 ? 'active' : ''">2024</button>
    </div>
    <br>
    <div class="grid-check">
      <div class="list-org">
        <label class="container-check fw">
          Organizaciones:
        </label>
        <label class="container-check ">C230 Consultores
          <input v-model="checkboxOrgCm" type="checkbox">
          <span class="checkmark"></span>
        </label>
        <label class="container-check ">Fundación IDEA
          <input v-model="checkboxOrgFi" type="checkbox">
          <span class="checkmark"></span>
        </label>
        <label class="container-check ">C230 Consulting
          <input v-model="checkboxOrgCu" type="checkbox">
          <span class="checkmark"></span>
        </label>
        <label class="container-check ">Supernova
          <input v-model="checkboxOrgSu" type="checkbox">
          <span class="checkmark"></span>
        </label>
      </div>
      <div class="empty-space">
      </div>
    </div>


    <div class="table-responsive">
      <table class="table table-hover">
        <thead>
          <tr class="table-info-new">
            <th width="20%" scope="col" class="fh">
              <div style="color: #3d70b3;">
                Nombre del colaborador
              </div>
              <div style="color: rgb(72 148 183 / 1);">
                Cargo
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Organización </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Días generados</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Vacaciones fijas</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Vacaciones flexibles</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Días disponibles</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-for="(t, index) in data">
            <tr :key="t.id" class="text-al border-n" >
              <template v-if="t.organizacion != null">
                <template v-if="
                (checkboxOrgCm == true && (t.organizacion).search('C230 Consultores') >= 0)
                || (checkboxOrgFi == true && (t.organizacion).search('IDEA') >= 0)
                || (checkboxOrgCu == true && (t.organizacion).search('C230 Consulting') >= 0)
                || (checkboxOrgSu == true && (t.organizacion).search('Supernova') >= 0)">
                <td class="fw">
                  <button v-show="t.id != idNav" class="btn btn-sm" @click="idNav = t.id;" style="background-color: transparent !important;"><i class="fa fa-angle-down"></i></button>
                  <button v-show="t.id == idNav" class="btn btn-sm" @click="idNav = 0;" style="background-color: transparent !important;"><i class="fa fa-angle-up"></i></button>
                  {{t.name}}
                </td>
                <td>{{t.organizacion}}</td>
                <td>{{t.date_in}}</td>
                <template v-for="(detail, index) in t.vacationabsencesdetail">
                  <td :key="'one' + index">{{parseFloat(detail.flexible_holidays) + parseFloat(detail.permanent_holidays) + parseFloat(detail.days_replacement)}}</td>
                  <td :key="'two' + index">{{parseFloat(detail.permanent_holidays)}}</td>
                  <td :key="'three' + index">{{parseFloat(detail.flexible_holidays)}}</td>
                  <td :key="'four' + index">{{parseFloat(detail.days_generated)}}</td>
                </template>
                <td></td>
              </template>
            </template>
          </tr>
          <tr v-show="t.id == idNav" :key="'child' + index">
            <template v-if="t.organizacion != null">
              <template v-if="
              (checkboxOrgCm == true && (t.organizacion).search('C230 Consultores') >= 0)
              || (checkboxOrgFi == true && (t.organizacion).search('IDEA') >= 0)
              || (checkboxOrgCu == true && (t.organizacion).search('C230 Consulting') >= 0)
              || (checkboxOrgSu == true && (t.organizacion).search('Supernova') >= 0)">
              <td colspan="8" style="border: 1px solid red;">
                <ul class="nav nav-pills mb-3" id="pills-tab" role="tablist">
                  <li class="nav-item" role="presentation">
                    <a class="nav-link active" @click="navOption = 1;" data-toggle="pill" href="#pills-home" role="tab" aria-controls="pills-home" aria-selected="true">Historial</a>
                  </li>
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" @click="navOption = 2;getRequest(t.id)" data-toggle="pill" href="#pills-profile" role="tab" aria-controls="pills-profile" aria-selected="false">Solicitudes</a>
                  </li>
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" @click="navOption = 3;" data-toggle="pill" href="#pills-contact" role="tab" aria-controls="pills-contact" aria-selected="false">Editar</a>
                  </li>
                </ul>
                <div class="tab-content" id="pills-tabContent">
                  <div class="tab-pane fade" :class="navOption == 1 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-home-tab">
                    <vacation-history v-if="navOption == 1 && t.id == idNav" :userId="t.id" :year="year" :origin="'Admin'"></vacation-history>
                  </div>
                  <div class="tab-pane fade" :class="navOption == 2 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-profile-tab">
                    <div class="card">
                      <div class="card-body">
                        <div class="grid-buttons" >
                          <button v-for="(r, index) in request"  :key="r.id" type="button" @click="dataRequest = r;classBtnRequest = index;" class="btn btn-outline-info btn-w btn-pd fw" :class="classBtnRequest == index ? 'active' : ''">Solicitud {{index + 1}}</button>
                        </div>
                        <template v-if="dataRequest != null">
                          <hr class="hr">
                          <p class="p-admin pd-text" style="color: #6C757D !important;">Supervisor(a) :</p>
                          <p style="color: #000;" class="pd-text text-al text-f fw">
                            {{dataRequest.name}}
                          </p>
                          <hr class="hr">
                          <div class="grid-inputs-one">
                            <div class="form-group text-al">
                              <label class="fw">Tipo de ausencia</label>
                              <select v-model="dataRequest.type" class="form-control">
                                <option value="0">Selecciona una opción</option>
                                <option value="1">Vacaciones</option>
                                <option value="2">Vacaciones (medio turno)</option>
                                <option value="3">Enfermedad</option>
                                <option value="4">Otro</option>
                              </select>
                            </div>
                            <div class="form-group text-al">
                              <label class="fw">Rango de fechas</label>
                              <div class="grid-inputs-two">
                                <input v-model="dataRequest.date_start" type="date" class="form-control" placeholder="Example input">
                                <input v-model="dataRequest.date_end" type="date" class="form-control" placeholder="Example input">
                              </div>
                            </div>

                          </div>
                          <div class="form-group text-al">
                            <label class="fw">Comentario</label>
                            <textarea v-model="dataRequest.comment" class="form-control"
                            rows="3"></textarea>
                          </div>
                          <div class="form-group text-al">
                            <label class="fw">Notas de supervisor(a)</label>
                            <textarea v-model="dataRequest.supervisor_note" class="form-control"
                            rows="3"></textarea>
                          </div>
                          <div class="grid-inputs-two m-lr" style="gap: 4%;" v-if="dataRequest.status == 1">
                            <button @click="requestHoliday(2)" type="button" class="btn btn-outline-success btn-rounded">
                              <img src="images/Solicitar.png">
                              Aprobar
                            </button>
                            <button @click="requestHoliday(3)" type="button" class="btn btn-outline-danger btn-rounded">
                              <img src="images/cancelar.png">
                              Rechazar
                            </button>
                          </div>
                          <div class="grid-inputs-two m-lr" style="gap: 4%;" v-else>
                            <template v-if="dataRequest.status == 2">
                              <button type="button" class="btn btn-outline-info">Aprobado</button>
                            </template>
                            <template v-if="dataRequest.status == 3">
                              <button type="button" class="btn btn-outline-danger">Rechazado</button>
                            </template>
                          </div>
                        </template>
                      </div>
                    </div>

                  </div>
                  <div class="tab-pane fade" :class="navOption == 3 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-contact-tab">
                    .3..
                  </div>
                </div>
              </td>
            </template>
          </template>
        </tr>
      </template>
    </tbody>
  </table>
</div>
</div>

</template>
<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
import VacationHistory from "@/views/vacation/VacationHistory.vue";
import {Modal} from "ant-design-vue";
import axios from "axios";

@Component({
  components: {VacationHistory }
})

export default class Welcome extends Vue {
  userName = "";
  check = "";

  checkboxOrgCm = true;
  checkboxOrgCu = false;
  checkboxOrgFi = false;
  checkboxOrgSu = false;

  data: Array<any> = [];
  history: any = [];
  request: Array<any> = [];
  dataRequest: any = null;

  year = 2022;
  yeatHistory = 2022;

  navOption = 1;
  idNav = 0;
  classBtnRequest: any = -1;

  async getRequest(id: any) {
    const request = await axios.get('/api/user-request-vacation/' + id + '&' + this.year);
    this.request = request.data;
  }

  async getHistory(id: any) {
    const history = await axios.get('/api/user-history-vacation/' + id + '&' + this.yeatHistory);
    this.history = history.data;
  }

  async getUsersActive(){
    const response = await axios.get('/api/get-list-users-active');
    this.data = response.data;
  }

  async requestHoliday(status: any) {
    const result: any = await axios.post('/api/admin-request-holiday',
    { id : this.dataRequest.id,
      supervisorNote : this.dataRequest.supervisor_note,
      status : status
    });
    if (result.data) {
      console.log('resuldar');
      this.dataRequest = result.data.data;
      console.log(result.data);

    }
    const success = Modal.success;
    success({
      title: "Tu información se ha guardado correctamente" ,
      content: '',
      okText: 'Aceptar',
    });
  }

  async mounted() {
    this.userName = this.$store.state.user.name;
    this.check = this.$store.state.user.pronombre;
    this.getUsersActive();
  }
}
</script>
<style media="screen">

.custom-h5{
  font-size: 2em !important;
  font-family: IBM Plex Sans Condensed;
  color: #50504f;
  font-weight: 600;
}

.fh {
  font-size: 0.9em;
}

.table-info-new {
  background: #f4f7f9;
}
</style>
<style media="screen">
.list-org {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.grid-check {
  display: grid;
  grid-template-columns: 80% 20%;
}

.container-check {
  display: block;
  position: relative;
  padding-left: 35px;
  margin-bottom: 12px;
  cursor: pointer;
  /* font-size: 22px; */
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}
/* Hide the browser's default checkbox */
.container-check input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: 0;
  left: 0;
  height: 15px;
  width: 15px;
  background-color: #eee;
}

/* On mouse-over, add a grey background color */
.container-check:hover input ~ .checkmark {
  background-color: #ccc;
}

/* When the checkbox is checked, add a blue background */
.container-check input:checked ~ .checkmark {
  background-color: #2196F3;
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: "";
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.container-check input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.container-check .checkmark:after {
  left: 5px;
  top: 2px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 2px 1px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

</style>
