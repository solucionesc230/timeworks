<template>
  <!-- <div> -->
  <div class="container-fluid" style="background: #FFFFFF;padding-right: 0px !important;padding-left: 0px !important;">
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
            <th  scope="col" class="fh">
              <div style="color: #3d70b3;">
                Nombre del colaborador
              </div>
            </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Organización </th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Fecha de ingreso</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Días generados</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Vacaciones fijas</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Vacaciones flexibles</th>
            <th scope="col" class="fh" style="color: #3d70b3; ">Días disponibles</th>
            <th width="15%" scope="col" class="fh" style="color: #3d70b3; ">Estatus</th>
          </tr>
        </thead>
        <tbody>
          <template v-if="data.length == 0">
            <tr>
              <td colspan="8">
                <div class="loadinghistory">
                </div>
                <h2>Cargando...</h2>
              </td>
            </tr>
          </template>
          <template v-for="(t, index) in data" >
            <tr :key="t.id" class="text-al border-n" :style="t.id == idNav ? 'background: #f0f6ff;' : ''">
              <template v-if="t.organizacion != null">
                <template v-if="
                (checkboxOrgCm == true && (t.organizacion).search('C230 Consultores') >= 0)
                || (checkboxOrgFi == true && (t.organizacion).search('IDEA') >= 0)
                || (checkboxOrgCu == true && (t.organizacion).search('C230 Consulting') >= 0)
                || (checkboxOrgSu == true && (t.organizacion).search('Supernova') >= 0)">
                <td class="fw" style="padding:0.5rem !important;display: flex;align-items: center;">
                  <button v-show="t.id != idNav" class="btn btn-sm" @click="idNav = t.id;dataRequest = null;request = [];navOption = 1;" style="background-color: transparent !important;padding: .25rem .3rem !important;"><i class="fa fa-angle-down"></i></button>
                  <button v-show="t.id == idNav" class="btn btn-sm" @click="idNav = 0;" style="background-color: transparent !important;padding: .25rem .4rem !important;"><i class="fa fa-angle-up"></i></button>
                  {{t.name}}
                </td>
                <td>{{t.organizacion}}</td>
                <td>{{t.date_in}}</td>
                <template v-if="(t.vacationabsencesdetail).length != 0">
                  <template v-for="(detail, index) in t.vacationabsencesdetail">
                    <template v-if="detail.active == 1">
                      <td style="text-align: center;" :key="'one' + index">{{parseFloat(detail.flexible_holidays) + parseFloat(detail.permanent_holidays) }}</td>
                      <td style="text-align: center;" :key="'two' + index">{{parseFloat(detail.permanent_holidays)}}</td>
                      <td style="text-align: center;" :key="'three' + index">{{parseFloat(detail.flexible_holidays)}}</td>
                      <td style="text-align: center;" :key="'four' + index" class="fw">{{parseFloat(detail.days_generated) }}</td>
                    </template>
                  </template>
                </template>
                <template v-else>
                  <td ></td>
                  <td ></td>
                  <td ></td>
                  <td ></td>
                </template>
                <td>
                  <template v-if="(t.vacationabsencesrequest).length != 0">
                    <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 1">
                      <button style="width:max-content;" class="span-revision">
                        Solicitud recibida
                      </button>
                    </template>
                    <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['status'] == 2">
                      <template v-if="t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_start'] >= this.dateNow && t.vacationabsencesrequest[(t.vacationabsencesrequest).length - 1]['date_end'] <= this.dateNow">
                        <button style="width:max-content;" class="span-finalizado">
                          Ausencia
                        </button>
                      </template>
                      <template v-else>
                        <button style="width:max-content;" class="span-aprobado">
                          Presente
                        </button>
                      </template>
                    </template>
                  </template>
                  <template v-else>
                    <button style="width:max-content;" class="span-aprobado">
                      Presente
                    </button>
                  </template>
                </td>
              </template>
            </template>
          </tr>
          <!-- Chid -->
          <tr v-show="t.id == idNav" :key="'child' + index" style="background: #f0f6ff;">
            <template v-if="t.organizacion != null">
              <!-- Filter companies -->
              <template v-if="
              (checkboxOrgCm == true && (t.organizacion).search('C230 Consultores') >= 0)
              || (checkboxOrgFi == true && (t.organizacion).search('IDEA') >= 0)
              || (checkboxOrgCu == true && (t.organizacion).search('C230 Consulting') >= 0)
              || (checkboxOrgSu == true && (t.organizacion).search('Supernova') >= 0)">

              <td colspan="8" style="border: 1px solid red;">
                <ul class="nav nav-pills mb-3" id="pills-tab" role="tablist">
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 1 ? 'active' : ''" @click="navOption = 1;" data-toggle="pill" href="#pills-home" role="tab" aria-controls="pills-home" aria-selected="true">Historial</a>
                  </li>
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 2 ? 'active' : ''" @click="navOption = 2;getRequest(t.id)" data-toggle="pill" href="#pills-profile" role="tab" aria-controls="pills-profile" aria-selected="false">Solicitudes</a>
                  </li>
                  <li class="nav-item" role="presentation">
                    <a class="nav-link" :class="navOption == 3 ? 'active' : ''" @click="navOption = 3;setInitialStatus(t.vacationabsencesdetail);" data-toggle="pill" href="#pills-contact" role="tab" aria-controls="pills-contact" aria-selected="false">Editar</a>
                  </li>
                </ul>
                <div class="tab-content" id="pills-tabContent">
                  <!-- Historial -->
                  <div class="tab-pane fade" :class="navOption == 1 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-home-tab">
                    <vacation-history v-if="navOption == 1 && t.id == idNav" :userId="t.id" :year="year" :origin="'Admin'"></vacation-history>
                  </div>
                  <!-- End historial -->
                  <!-- Solicitudes -->
                  <div class="tab-pane fade" :class="navOption == 2 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-profile-tab">
                    <div class="card card-shadow">
                      <div class="card-body">
                        <div v-show="loadingRequest">
                          <div class="loading">
                          </div>
                          <h2>Cargando...</h2>
                        </div>
                        <div v-show="!loadingRequest">
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
                            <div class="grid-inputs-one-admin">
                              <div class="form-group text-al">
                                <label class="fw">Tipo de ausencia</label>
                                <select disabled v-model="dataRequest.type" class="form-control">
                                  <option value="0">Selecciona una opción</option>
                                  <option value="1">Vacaciones</option>
                                  <option value="2">Vacaciones (medio turno)</option>
                                  <option value="3">Enfermedad</option>
                                  <option value="4">Otro</option>
                                  <option value="5">Días no pagados</option>
                                </select>
                                <template v-if="dataRequest.type == 4">
                                  <input disabled v-model="dataRequest.commentother" type="text" class="form-control" placeholder="Escriba un comentario" style="margin-top: 2%;">
                                </template>
                              </div>
                              <div class="form-group text-al">
                                <label class="fw">Días solicitados</label>
                                <input disabled type="text" v-model="dataRequest.days" class="form-control">
                              </div>
                              <div class="form-group text-al">
                                <label class="fw">Rango de fechas</label>
                                <div class="grid-inputs-two">
                                  <div class="input-group mb-3">
                                    <div class="input-group-prepend">
                                      <span class="input-group-text" style="font-size:small;">Inicio:</span>
                                    </div>
                                    <input disabled v-model="dataRequest.date_start" type="date" class="form-control" >
                                  </div>
                                  <div class="input-group mb-3">
                                    <div class="input-group-prepend">
                                      <span class="input-group-text" style="font-size:small;">Fin:</span>
                                    </div>
                                    <input disabled v-model="dataRequest.date_end" type="date" class="form-control" >
                                  </div>
                                </div>
                              </div>

                            </div>
                            <div class="form-group text-al">
                              <label class="fw">Comentario compartido</label>
                              <textarea disabled v-model="dataRequest.comment" class="form-control"
                              rows="3"></textarea>
                            </div>
                            <div class="form-group text-al">
                              <label class="fw">Notas de supervisor(a)</label>
                              <textarea v-model="dataRequest.supervisor_note" class="form-control"
                              rows="3"></textarea>
                            </div>
                            <div class="grid-inputs-two m-lr" style="gap: 4%;" v-if="dataRequest.status == 1">
                              <button v-if="permission" @click="requestHoliday(2)" type="button" class="btn btn-outline-success btn-rounded">
                                <img src="images/Solicitar.png">
                                Aprobar
                              </button>
                              <button v-if="permission" @click="requestHoliday(3)" type="button" class="btn btn-outline-danger btn-rounded">
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

                  </div>
                  <!-- End Solicitudes -->
                  <!-- Detalles -->
                  <div class="tab-pane fade" :class="navOption == 3 ? 'show active' : ''" role="tabpanel" aria-labelledby="pills-contact-tab">
                    <div class="card card-shadow">
                      <div class="card-body">
                        <div class="loading" v-show="loadingRequest">

                        </div>
                        <div v-show="!loadingRequest">
                          <div class="grid-buttons" >
                            <button type="button" class="btn btn-outline-info btn-w btn-pd fw" @click="year = 2022;" :class="year == 2022 ? 'active' : ''">{{year}}</button>
                            <!-- <button type="button" class="btn btn-outline-info btn-center btn-w btn-pd fw" @click="year = 2023;" :class="year == 2023 ? 'active' : ''">2023</button> -->
                            <!-- <button type="button" class="btn btn-outline-info btn-right btn-w btn-pd fw" @click="year = 2024;" :class="year == 2024 ? 'active' : ''">2024</button> -->
                          </div>
                          <div class="grid-dates">

                            <div class="card-days-three">
                              <template v-for="(detail, index) in t.vacationabsencesdetail">
                                <div v-if="detail.active == 1" :key="'ph' + index">

                                  <div class="card-three-sub-one">
                                    <div class="grid-buttons-up-down">
                                      <span class="icon square arrow up"></span>
                                      <span class="icon square arrow down"></span>
                                    </div>
                                    <div>
                                      <p class="m0 fw color-black">
                                        <template v-for="(detail, index) in t.vacationabsencesdetail">
                                          <div v-if="detail.active == 1" :key="index">
                                            {{(parseFloat(detail.permanent_holidays) + parseFloat(detail.flexible_holidays)) }} días generados
                                          </div>
                                        </template>
                                      </p>
                                      <p class="m0 fw">Desde: {{fechaSince(t.date_in)[0] == true ? fechaSince(t.date_in)[1] : fechaSince(t.date_in)[1] }} </p>
                                    </div>
                                  </div>
                                  <div class="card-three-sub-two">
                                    <p>&nbsp;</p>
                                    <div class="card-three-sub-three">
                                      <div class="grid-buttons-up-down">
                                        <span class="icon square arrow up"
                                        @click="edit += 1; initialStatusVacationFi += 1;
                                        detail.permanent_holidays = parseFloat(detail.flexible_holidays) == 0 ? parseFloat(detail.permanent_holidays) : parseFloat(detail.permanent_holidays) + 1;
                                        detail.flexible_holidays = parseFloat(detail.flexible_holidays) == 0 ? 0 : parseFloat(detail.flexible_holidays) - 1;">
                                      </span>
                                      <span class="icon square arrow down" @click="edit += 1; initialStatusVacationFi -= 1;
                                      detail.permanent_holidays = parseFloat(detail.permanent_holidays) == 0 ? parseFloat(detail.permanent_holidays) : parseFloat(detail.permanent_holidays) - 1;
                                      detail.flexible_holidays = parseFloat(detail.permanent_holidays) == 0 ?  parseFloat(initialVacationFi + initialVacationFl) : parseFloat(detail.flexible_holidays) + 1"></span>
                                    </div>
                                    <p class="m0 fw color-black">
                                      {{parseFloat(detail.permanent_holidays)}} vacaciones fijas
                                    </p>
                                  </div>
                                  <p>&nbsp;</p>
                                  <div class="card-three-sub-three">
                                    <div class="grid-buttons-up-down">
                                      <span class="icon square arrow up"
                                      @click="edit += 1; initialStatusVacationFl += 1;
                                      detail.flexible_holidays = parseFloat(detail.permanent_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) + 1;
                                      detail.permanent_holidays = parseFloat(detail.permanent_holidays) == 0 ? 0 : parseFloat(detail.permanent_holidays) - 1;">
                                    </span>
                                    <span class="icon square arrow down"
                                    @click="edit += 1; initialStatusVacationFl -= 1;
                                    detail.flexible_holidays = parseFloat(detail.flexible_holidays) == 0 ? parseFloat(detail.flexible_holidays) : parseFloat(detail.flexible_holidays) - 1;
                                    detail.permanent_holidays = parseFloat(detail.flexible_holidays) == 0 ?  parseFloat(initialVacationFi + initialVacationFl) : parseFloat(detail.permanent_holidays) + 1;">
                                  </span>
                                </div>
                                <p class="m0 fw color-black">
                                  {{parseFloat(detail.flexible_holidays )}} vacaciones flexibles
                                </p>
                              </div>
                            </div>

                          </div>
                        </template>
                      </div>
                      <div class="card-days-three">
                        <template v-for="(detail, index) in t.vacationabsencesdetail">
                          <div v-if="detail.active == 1" :key="index">
                            <div class="card-three-sub-four" style="margin-bottom: 6.6%;">
                              <p class="m0 fw color-black">
                                {{parseFloat(detail.days_replacement )}} días reposición
                              </p>
                              <div class="buttons-grid-sr" @click="visible = true;detailId = detail.id;">
                                + / -
                              </div>


                            </div>
                            <div class="card-three-sub-two">
                              <table>
                                <tr>
                                  <th style="color:#6236ff;">Días</th>
                                  <th style="color:#6236ff;">Notas</th>
                                </tr>
                                <tr v-for="vard in detail.vacationabsencesreplacementdays"  :key="vard.id">
                                  <td>{{(vard.type == 1 ? '+' : vard.type == 2 ? '-' : '')  + parseFloat(vard.days)}}</td>
                                  <td>{{vard.note}}</td>
                                </tr>
                              </table>
                            </div>
                          </div>
                        </template>
                      </div>

                      <br>
                    </div>
                    <div class="text-footer">
                      <div>
                        <p class="mtb0 fw color-black" style="font-size: 1.2rem;">
                          <template v-for="(detail, index) in t.vacationabsencesdetail">
                            <div v-if="detail.active == 1" :key="index">
                              {{parseFloat(detail.days_generated) }} días disponibles
                            </div>
                          </template>
                        </p>
                        <p class="mtb0 fw">Válidos hasta el 31/03/23</p>
                      </div>
                      <div class="grid-buttons-plus" v-show="edit > 0">

                        <button type="button" class="btn btn-outline-primary" style="height: 50%; align-self: center; margin-left: 2%;" name="button" @click="saveDaysUpdate(t.vacationabsencesdetail)">Guardar cambios</button>
                        <button type="button" class="btn btn-outline-danger" style="height: 50%; align-self: center; margin-left: 1%;"
                        @click="t.vacationabsencesdetail[0].flexible_holidays = initialVacationFl;t.vacationabsencesdetail[0].permanent_holidays =  initialVacationFi; edit = 0;" name="button">
                        Cancelar
                      </button>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- End detalles -->
        </div>
      </td>
    </template><!--End of filter-->
  </template>
</tr>
<!-- End child -->
</template>
</tbody>
</table>
</div>
<a-modal v-model="visible" :title="typeDayAdd == 1 ? 'Agregar días': typeDayAdd == 2 ? 'Restar días' : ''" @ok="handleOk">
  <div v-if="!loadingModal">
    <div class="row">
      <legend class="col-form-label col-sm-2 pt-0">Tipo</legend>
      <div class="col-sm-10">
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio1" v-model="typeDayAdd" value="1">
          <label class="form-check-label" for="inlineRadio1">Sumar días</label>
        </div>
        <div class="form-check form-check-inline">
          <input class="form-check-input" type="radio" name="inlineRadioOptions" id="inlineRadio2" v-model="typeDayAdd" value="2">
          <label class="form-check-label" for="inlineRadio2">Restar días</label>
        </div>
      </div>
    </div>
    <label>Días</label>
    <input type="number" class="form-control" v-model="daysr">
    <label>Nota</label>
    <input type="text" class="form-control" v-model="noter">
  </div>
  <div class="loading" v-show="loadingModal">
  </div>
</a-modal>
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

  edit = 0;

  initialStatusVacationFi = 0;
  initialStatusVacationFl = 0;
  initialStatusVacationDg = 0;

  initialVacationFi = 0;
  initialVacationFl = 0;

  visible = false;
  loadingRequest = false;
  loadingModal = false;

  typeDayAdd = 1;//1 suma, 2 resta
  detailId = 0;
  daysr = 0;
  noter = '';

  role: any = 0;
  permission: any = false;

  dateNow: any = '';

  setInitialStatus(data: any){
    this.initialVacationFi = parseFloat(data[0]['permanent_holidays']);
    this.initialVacationFl = parseFloat(data[0]['flexible_holidays']);
  }

  fechaSince(data: any){
    const year = new Date();
    // const date = new Date();
    // const date_in = new Date(data);
    //
    if (data > year.getFullYear() + '-04-01') {
      return [false, data];
    }else{
      return [true, year.getFullYear() + '-04-01'];
    }
  }

  getPermission(data: any){
    const roles = JSON.parse(this['$store'].state.user.roles_time);
    const array = roles.map(function(x) {
      return x.id;
    });
    let iguales=0;

    for(let i=0; i < data.length; i++)
    {
      for(let j=0; j < array.length; j++)
      {
        if(data[i]==array[j])
        iguales++;
      }
    }
    if (iguales > 0) {
      this.permission = true;
    }else {
      this.permission = false;
    }
  }

  async handleOk() {

    if (this.noter == '') {
      this.showAlert('Se necesita registrar un comentario');
      return;
    }
    if (this.daysr == 0) {
      this.showAlert('Los días no pueden ser 0');
      return;
    }
    this.loadingModal = true;
    const result: any = await axios.post('/api/save-detail-holiday',
    { id : this.detailId,
      type : this.typeDayAdd,
      day : this.daysr,
      note : this.noter
    });

    if (result.data.status) {
      this.getUsersActive();
      this.visible = false;
      this.daysr = 0;
      this.noter = '';
      this.loadingModal = false;
    }else{
      this.showAlert(result.data.error);
      this.loadingModal = false;
    }
  }

  async getRequest(id: any) {
    this.getPermission([2,4,5]);
    this.loadingRequest = true;
    const request = await axios.get('/api/user-request-vacation/' + id + '&' + this.year);
    this.request = request.data;
    this.loadingRequest = false;

  }

  async getHistory(id: any) {
    const history = await axios.get('/api/user-history-vacation/' + id + '&' + this.yeatHistory);
    this.history = history.data;
  }

  async getUsersActive(){
    this.data = [];
    const response = await axios.get('/api/get-list-users-active');
    this.data = response.data;
  }

  showAlert(title: string) {
    const confirm = Modal.info;
    confirm({
      title: title ,
      content: '',
      okText: 'Aceptar',
    });
  }

  async requestHoliday(status: any) {
    if (this.dataRequest.supervisor_note == null || this.dataRequest.supervisor_note == "") {
      this.showAlert("El campo Notas de supervisor(a) es requerido");
      return;
    }
    this.loadingRequest = true;
    const result: any = await axios.post('/api/admin-request-holiday',
    { id : this.dataRequest.id,
      supervisorNote : this.dataRequest.supervisor_note,
      status : status
    });
    if (result.data) {
      this.dataRequest = result.data.data;
      this.loadingRequest = false;
    }
    const success = Modal.success;
    success({
      title:  status == 2 ? 'Aprobado' : status == 3 ? 'Rechazado' : '',
      content: '',
      okText: 'Aceptar',
    });
  }

  async saveDaysUpdate(data: any){
    this.loadingRequest = true;
    const result: any = await axios.post('/api/save-days-update', data[0]);
    if (result.data) {
      this.loadingRequest = false;
    }
    const success = Modal.success;
    success({
      title:  'información actualizada',
      content: '',
      okText: 'Aceptar',
    });
  }

  async mounted() {
    this.userName = this.$store.state.user.name;
    this.check = this.$store.state.user.pronombre;
    this.getUsersActive();
    this.role = this.$store.state.user.role_time_id;
    this.dateNow = new Date().toISOString().slice(0, 10);
    // this.getPermission();
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

.grid-inputs-one-admin {
  display: grid;
  grid-template-columns: 25% 25% 50%;
  gap : 5px;
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

.grid-dates {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.5%;
  border-top: 1px solid #000;
  border-bottom: 1px solid #000;
  margin-top: 1%;
}

.card-days {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-days-two {
  border-top: 4px solid #6236ff;
  margin-top: 4%;
  text-align: left;
  display: grid;
  grid-template-columns: 78% 15%;
  height: min-content;
}

.card-days-three {
  text-align: left;
  display: grid;
  grid-template-columns: 1fr;
  height: min-content;
  border-top: 4px solid #6236ff;
  margin-top: 4%;
}

.card-three-sub-one {
  text-align: left;
  display: grid;
  grid-template-columns: 10% 90%;
  height: min-content;
}

.card-three-sub-four {
  text-align: left;
  display: grid;
  grid-template-columns: 90% 10%;
  height: min-content;
}

.card-three-sub-two {
  text-align: left;
  display: grid;
  grid-template-columns: 8% 92%;
  height: min-content;
  background: #ebe8f6;
}

.card-three-sub-three {
  text-align: left;
  display: grid;
  grid-template-columns: 6% 94%;
  height: min-content;
  background: #ebe8f6;
}

.text-footer {
  display: flex;
  flex-direction: column;
  /* justify-content: flex-end; */
  align-items: flex-end;
}

.m0 {
  margin-bottom: 0;
  margin-top: 2%;
  font-size: 1em;
}

.mtb0 {
  margin-bottom: 0;
  margin-top: 0;
}

.color-black {
  color: #000000;
}

span.square{
  background: transparent;
  border-radius:5px;
  border-bottom:1px solid #FFF;
  display:inline-block;
  height:10px;
  width:10px;
  cursor: pointer;
}
.arrow{
  position:relative;
}
.arrow.up:after{
  position:absolute;
  top:7px;
  left:7px;
  width: 0;
  height: 0;
  content:'';
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent #000000 transparent;
  border-radius:2px;
}

.arrow.up:before{
  position:absolute;
  top:11px;
  left:7px;
  width: 0;
  height: 0;
  content:'';
  border-style: solid;
  border-width: 0 7px 8px 7px;
  border-color: transparent transparent transparent transparent;
  border-radius:2px;
}

.arrow.down:after{
  position:absolute;
  top:10px;
  left:7px;
  width: 0;
  height: 0;
  content:'';
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: #000000 transparent transparent transparent;
  border-radius:2px;
}

.arrow.down:before{
  position:absolute;
  top:12px;
  left:7px;
  width: 0;
  height: 0;
  content:'';
  border-style: solid;
  border-width: 8px 7px 0 7px;
  border-color: transparent transparent transparent transparent;
  border-radius:2px;
}

.grid-buttons-up-down {
  display: flex;
  flex-direction: column;
}

.grid-buttons-plus {
  display: flex;
  flex-direction: row;
  align-items: center;
}

.btn-plus {
  border-radius: 80%;
  height: 80%;
  border: 1px solid black;
  background: transparent;
  padding: 0;
  padding-left: 12%;
  padding-right: 12%;
  padding-bottom: 2%;
  padding-top: 2%;
  margin-left: 4%;
  font-size: 4rem;
}

.circle{
  width: 60%;
  height: 60%;
  border: 1px solid #000;
  border-radius: 100%;
  -moz-border-radius: 100%;
  -webkit-border-radius: 100%;
  text-align: center;
  font: small-caption;
  font-weight: bold;
  cursor: pointer;
}

/*  */
.loadinghistory {
  display: inline-block;
  width: 80px;
  height: 80px;
  border: 5px solid rgb(26 108 97 / 56%);
  border-radius: 50%;
  border-top-color: #fff;
  animation: spinh 1s ease-in-out infinite;
  -webkit-animation: spinh 1s ease-in-out infinite;
  position: inherit;
  top: 50%;
  left: 56%;
}

@keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}

@-webkit-keyframes spinh {
  to {
    -webkit-transform: rotate(360deg);
  }
}

/* Span buttons */
.span-aprobado {
  border: 1px solid #accf60;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #accf60;
  background: #f4f8e9;
}

.span-rechazado {
  border: 1px solid #db7e97;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #d63776;
  background: #f9e9ec;
}

.span-revision {
  border: 1px solid #c0c1c2;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #989da5;
  background: #eef0f2;
}

.span-finalizado {
  border: 1px solid #b0a1fa;
  padding-top: 2%;
  padding-bottom: 2%;
  padding-left: 14%;
  padding-right: 14%;
  border-radius: 20px;
  color: #836bf9;
  background: #f2f0fd;
}

.buttons-grid-sr {
  border: 1px solid #6236ff;
  text-align: center;
  align-self: end;
  background: #6236ff;
  border-radius: 15px;
  font-size: 14px;
  font-weight: 800;
  color: white;
  cursor: pointer;
  margin: 0 0px 0 -30%;
}
</style>
