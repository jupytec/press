<template>
	<SiteAppPlanSelectorDialog
		v-model="showDialog"
		:app="app"
		:currentPlan="currentPlan"
		@plan-select="onPlanSelect"
	/>
</template>

<script>
import { getToastErrorMessage } from '../../utils/toast';
import SiteAppPlanSelectorDialog from './SiteAppPlanSelectorDialog.vue';
import { toast } from 'vue-sonner';

export default {
	components: {
		SiteAppPlanSelectorDialog
	},
	props: ['app', 'currentPlan'],
	emits: ['plan-changed', 'plan-selected'],
	data() {
		return {
			showDialog: true
		};
	},
	resources: {
		changeAppPlan: {
			url: 'press.api.marketplace.change_app_plan'
		}
	},
	methods: {
		onPlanSelect(plan) {
			// if current plan is specified it's for changing plan
			// else it's for selecting plan while installing app
			if (this.currentPlan)
				toast.promise(
					this.$resources.changeAppPlan.submit({
						subscription: this.app.subscription.name,
						new_plan: plan.name
					}),
					{
						loading: 'Changing plan...',
						success: () => {
							this.$emit('plan-changed', plan);
							return 'Plan changed successfully';
						},
						error: e => getToastErrorMessage(e)
					}
				);
			else this.$emit('plan-selected', plan);
		}
	}
};
</script>
