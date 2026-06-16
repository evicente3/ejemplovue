<template>
  <div class="main-wrapper">
    <div class="container">
      <header class="document-header">
        <h1>CONSTANCIA DE MATRÍCULA DE LABORATORIO</h1>
        <h2>Escuela Profesional de Ingeniería de Sistemas EPIS</h2>
        <p class="date-emission">Emitido el: 11/06/2026</p>
      </header>

      <hr class="divider" />

      <div v-if="loading" class="status-msg">Cargando datos del servidor...</div>
      <div v-else-if="error" class="error-msg">{{ error }}</div>

      <div v-else>
        <section class="section-box">
          <div class="section-title">DATOS DEL ALUMNO</div>
          <div class="info-grid">
            <div class="info-label">C.U.I.:</div>
            <div class="info-value">{{ alumno.cui }}</div>

            <div class="info-label">Nombre completo:</div>
            <div class="info-value font-uppercase">{{ alumno.full_name }}</div>

            <div class="info-label">Email:</div>
            <div class="info-value">{{ alumno.email }}</div>
          </div>
        </section>

        <section class="section-box">
          <div class="section-title">ASIGNATURAS MATRICULADAS</div>
          <table class="academic-table">
            <thead>
              <tr>
                <th style="width: 5%;">Nº</th>
                <th style="width: 12%;">Código</th>
                <th style="width: 43%;">Curso</th>
                <th style="width: 10%;">Año</th>
                <th style="width: 8%;">Grupo</th>
                <th style="width: 12%;">Laboratorio</th>
                <th style="width: 10%;">Docente</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, index) in matriculas" :key="item.id">
                <td class="text-center">{{ index + 1 }}</td>
                <td>{{ item.workload.course.code }}</td>
                <td>
                  <span class="course-name">{{ item.workload.course.name }}</span>
                  <span class="course-acronym"> ({{ item.workload.course.acronym }})</span>
                </td>
                <td>{{ item.workload.course.year_display }}</td>
                <td class="text-center">{{ item.workload.group }}</td>
                <td>{{ item.workload.laboratory }}</td>
                <td class="font-uppercase">{{ item.workload.teacher.full_name }}</td>
              </tr>
            </tbody>
          </table>
        </section>

        <footer class="document-footer">
          <p class="total-courses"><strong>Total de cursos matriculados:</strong> {{ matriculas.length }}</p>
          <p class="legal-notice">Documento generado digitalmente por el Sistema de Matrícula de Laboratorio EPIS.</p>
        </footer>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';

const matriculas = ref([]);
const loading = ref(true);
const error = ref(null);
const cui = '20250100';

const alumno = computed(() => {
  return matriculas.value.length > 0 ? matriculas.value[0].student : {};
});

const fetchConstancia = async () => {
  try {
    loading.value = true;
    const response = await axios.get(`https://sisacad-enrollments-backend.vercel.app/restful/enrollment-certificate/?cui=${cui}`);
    matriculas.value = response.data.results;
  } catch (err) {
    console.error(err);
    error.value = 'Error al conectar con la API de SisAcad.';
  } finally {
    loading.value = false;
  }
};

onMounted(() => {
  fetchConstancia();
});
</script>

<style scoped>
.main-wrapper {
  background-color: #ffffff;
  min-height: 100vh;
  padding: 40px 20px;
  color: #333333;
}
.container {
  max-width: 1000px;
  margin: 0 auto;
  font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}
.document-header {
  text-align: center;
  margin-bottom: 10px;
}
.document-header h1 {
  color: #0d47a1;
  font-size: 24px;
  font-weight: bold;
  margin: 0 0 8px 0;
  letter-spacing: 0.5px;
}
.document-header h2 {
  color: #555555;
  font-size: 18px;
  font-weight: 600;
  margin: 0 0 10px 0;
}
.date-emission {
  color: #666666;
  font-size: 13px;
  margin: 0;
}
.divider {
  border: 0;
  border-top: 1px solid #999999;
  margin: 15px 0 25px 0;
}
.section-box {
  margin-bottom: 30px;
}
.section-title {
  border-left: 4px solid #0d47a1;
  background-color: #f2f2f2;
  padding: 8px 12px;
  font-weight: bold;
  font-size: 14px;
  letter-spacing: 0.5px;
  color: #222222;
  margin-bottom: 15px;
}
.info-grid {
  display: grid;
  grid-template-columns: 180px 1fr;
  row-gap: 12px;
  padding-left: 10px;
  font-size: 14px;
}
.info-label {
  font-weight: bold;
  color: #222222;
}
.info-value {
  color: #444444;
}
.font-uppercase {
  text-transform: uppercase;
}
.academic-table {
  width: 100%;
  border-collapse: collapse;
  font-size: 13px;
  margin-top: 10px;
}
.academic-table th, .academic-table td {
  border: 1px solid #e0e0e0;
  padding: 10px 12px;
  text-align: left;
  vertical-align: middle;
}
.academic-table th {
  background-color: #ffffff;
  color: #333333;
  font-weight: bold;
}
.academic-table tr:nth-child(even) {
  background-color: #fafafa;
}
.course-name {
  font-weight: bold;
  color: #222222;
}
.course-acronym {
  color: #555555;
}
.text-center {
  text-align: center;
}
.document-footer {
  margin-top: 40px;
}
.total-courses {
  font-size: 14px;
  color: #222222;
  margin-bottom: 25px;
}
.legal-notice {
  text-align: center;
  font-style: italic;
  color: #999999;
  font-size: 12px;
  margin-top: 30px;
}
.status-msg, .error-msg {
  text-align: center;
  font-weight: bold;
  padding: 20px;
  font-size: 16px;
}
.status-msg { color: #0d47a1; }
.error-msg { color: #d32f2f; }
</style>
