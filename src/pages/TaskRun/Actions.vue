<script>
import { mapGetters } from 'vuex'
// import CancelButton from '@/components/CancelButton.vue'
import SetStateDialog from '@/components/SetStateDialog.vue'
import RestartDialog from '@/pages/TaskRun/Restart-Dialog'
import ResumeButton from '@/components/ResumeButton'

export default {
  components: { RestartDialog, SetStateDialog, ResumeButton },
  props: {
    taskRun: {
      required: true,
      type: Object
    }
  },
  data() {
    return {
      restartDialog: false
    }
  },
  computed: {
    ...mapGetters('tenant', ['tenant', 'role'])
  },
  methods: {}
}
</script>

<template>
  <div
    class="pa-0 mb-2 d-flex align-center"
    :class="$vuetify.breakpoint.smAndDown ? 'justify-center' : 'justify-end'"
  >
    <ResumeButton
      v-if="taskRun.state == 'Paused'"
      dialog-type="resume"
      :task-run="taskRun"
      :include-text="true"
    />
    <v-tooltip bottom>
      <template #activator="{ on }">
        <div v-on="on">
          <v-btn
            class="vertical-button mr-2"
            :style="{ height: '46px' }"
            text
            depressed
            small
            :disabled="role === 'READ_ONLY_USER'"
            color="primary"
            @click="restartDialog = true"
          >
            <v-icon>fab fa-rev</v-icon>
            <div>Restart</div>
          </v-btn>
        </div>
      </template>
      <span v-if="role === 'READ_ONLY_USER'">
        Read-only users cannot restart flow runs
      </span>
      <span v-else>Restart flow run from this task</span>
    </v-tooltip>

    <v-dialog v-model="restartDialog" width="500">
      <RestartDialog
        :task-run="taskRun"
        :flow-run-id="taskRun.flow_run.id"
        @cancel="restartDialog = false"
      />
    </v-dialog>

    <SetStateDialog dialog-type="task run" :task-run="taskRun" />

    <!-- Shouldn't we be able to cancel this? -->
    <!-- <CancelButton dialog-type="flow run" :flow-run="flowRun" /> -->
  </div>
</template>

<style lang="scss">
.vertical-divider {
  border-left: 0.5px solid rgba(0, 0, 0, 0.26);
  border-right: 0.5px solid rgba(0, 0, 0, 0.26);
  height: 75%;
}
</style>
