<template>
  <div>
    <Header class="bg-black-400">
      <template v-slot:content>
        <div class="d-flex flex-items-center w-100">
          <VIcon class="c-pointer" name="arrow-left" @click="goBack" />
          <div class="d-flex flex-auto flex-items-center ml-3">
            <CompanyLogo :url="form.title" />
            <span class="title fw-bold h5 ml-2">{{ form.title }}</span>
          </div>
          <div class="d-flex">
            <!-- Delete Btn -->
            <button v-tooltip="$t('Delete')" @click="onClickDelete">
              <VIcon class="c-pointer trash" name="trash" />
            </button>

            <!-- Edit Btn -->
            <button v-if="!isEditMode" v-tooltip="$t('Edit')" @click="isEditMode = true">
              <VIcon class="c-pointer ml-2 cogs" name="cogs" />
            </button>
          </div>
        </div>
      </template>
    </Header>
    <div class="scroll detail">
      <form class="form" @submit.stop.prevent="onClickUpdate">
        <FormRowText v-model="form.title" title="title" :edit-mode="isEditMode" :show-icons="false">
          <template v-slot:second-icon> <div /> </template>
        </FormRowText>
        <FormRowText v-model="form.email" title="email" :edit-mode="isEditMode" :show-icons="true">
          <template v-slot:second-icon> <div /> </template>
        </FormRowText>
        <FormRowText
          v-model="form.password"
          title="password"
          :edit-mode="isEditMode"
          :show-icons="true"
          password
        />
        <!-- Save & Cancel -->
        <div class="d-flex m-3" v-if="isEditMode">
          <VButton class="flex-1" theme="text" :disabled="loading" @click="isEditMode = false">
            {{ $t('Cancel') }}
          </VButton>
          <VButton class="flex-1" type="submit" :loading="loading">
            {{ $t('Save') }}
          </VButton>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { mapState, mapActions } from 'vuex'
import DetailMixin from '@/mixins/detail'

export default {
  mixins: [DetailMixin],

  data() {
    return {
      isEditMode: false,
      showPass: false
    }
  },

  beforeRouteUpdate(to, from, next) {
    this.isEditMode = false
    this.showPass = false
    next()
  },

  methods: {
    ...mapActions('Emails', ['Delete', 'Update']),

    openLink() {
      this.$browser.tabs.create({
        url: this.detail.url
      })
    },

    goBack() {
      this.$router.push({ name: 'Emails', params: { cache: true } })
    },

    onClickDelete() {
      const onSuccess = async () => {
        await this.Delete(this.form.id)
        const index = this.ItemList.findIndex(item => item.id == this.form.id)
        if (index !== -1) {
          this.ItemList.splice(index, 1)
        }
        this.$router.push({ name: 'Emails', params: { cache: true } })
      }
      if (confirm('Are you sure you want to delete'))
        this.$request(onSuccess, this.$waiters.Emails.Delete)
    },

    async onClickUpdate() {
      const onSuccess = async () => {
        await this.Update({ ...this.form })
        this.$router.push({ name: 'Emails', params: { cache: true } })
      }

      await this.$request(onSuccess, this.$waiters.Emails.Update)
      this.isEditMode = false
    }
  },
  computed: {
    ...mapState('Emails', ['Detail', 'ItemList']),

    loading() {
      return this.$wait.is(this.$waiters.Emails.Update)
    }
  }
}
</script>

<style lang="scss">
.trash {
  color: $color-danger;
}
.cogs {
  color: #ffffff;
}
.title {
  flex: 1;

  word-break: break-all;
}
</style>
