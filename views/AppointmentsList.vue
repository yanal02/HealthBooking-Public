<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/" class="btn btn-outline-primary">
          ⇆ Switch Page
        </router-link>
      </div>
    </nav>

    <div class="container">
      <div class="card shadow-sm">
        <div class="card-header">
          <h5 class="mb-0">Appointments</h5>
        </div>

        <div class="card-body p-0">
          <table class="table table-bordered table-striped mb-0">
            <thead class="table-light">
              <tr>
                <th>Name</th>
                <th>Symptoms</th>
                <th>Time</th>
                <th>Status</th>
                <th>Update</th>
              </tr>
            </thead>

            <tbody>
              <tr v-if="appointments.length === 0">
                <td colspan="5" class="text-center p-4">
                  No appointments found.
                </td>
              </tr>

              <tr
                v-for="appointment in appointments"
                :key="appointment.appointmentId"
              >
                <td>{{ appointment.patientName }}</td>
                <td>{{ appointment.symptoms }}</td>
                <td>{{ appointment.slot }}</td>
                <td>{{ appointment.status }}</td>
                <td>
                  <select
                    class="form-select"
                    :value="appointment.status"
                    @change="event => updateStatus(appointment, event.target.value)"
                  >
                    <option>Pending</option>
                    <option>In Progress</option>
                    <option>Completed</option>
                  </select>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const API_BASE_URL = "https://qwn0xa15p4.execute-api.eu-north-1.amazonaws.com";

export default {
  name: "AppointmentsList",

  data() {
    return {
      appointments: []
    };
  },

  mounted() {
    this.fetchAppointments();
  },

  methods: {
    async fetchAppointments() {
      try {
        const response = await fetch(`${API_BASE_URL}/appointments`);
        const data = await response.json();

        this.appointments = Array.isArray(data)
          ? data
          : JSON.parse(data.body || "[]");
      } catch (error) {
        console.error("Failed to fetch appointments:", error);
        alert("Failed to load appointments.");
      }
    },

    async updateStatus(appointment, newStatus) {
      try {
        const url = `${API_BASE_URL}/appointments/${appointment.appointmentId}`;

        const response = await fetch(url, {
          method: "PATCH",
          headers: {
            "Content-Type": "application/json"
          },
          body: JSON.stringify({
            status: newStatus
          })
        });

        const rawBody = await response.text();

        if (!response.ok) {
          throw new Error(`HTTP ${response.status}: ${rawBody}`);
        }

        alert("Status updated!");

        await this.fetchAppointments();
      } catch (error) {
        console.error("Failed to update status:", error);
        alert("Update failed. See console for details.");
      }
    }
  }
};
</script>
