<template>
	<div id="app">
		<div>
			<b-alert
				:show="dismissCountDown"
				:variant="variant"
				@dismissed="dismissCountDown = 0"
				@dismiss-count-down="countDownChanged"
			>
				{{ message }}
			</b-alert>
		</div>
		<AppHeader />
		<div>
			<b-button v-b-modal.modal-prevent-closing>Add Record</b-button>

			<b-modal
				id="modal-prevent-closing"
				ref="modal"
				title="Add License"
				@show="resetModal"
				@hidden="resetModal"
				@ok="handleOk"
			>
				<form ref="form" @submit.stop.prevent="handleSubmit">
					<b-form-group
						label="Number Plate"
						label-for="plateNumber-input"
						invalid-feedback="Number Plate is required"
						:state="PlateState"
					>
						<b-form-input
							id="plateNumber-input"
							v-model="plateNumber"
							placeholder="Plate Numbeer Eg. (AA-BB123)"
							:state="PlateState"
							required
						></b-form-input>
					</b-form-group>
					<b-form-group label="Start Date" label-for="start_date-input">
						<b-form-datepicker
							id="start_date-input"
							v-model="start_date"
							placeholder="Choose Start Date"
							class="mb-2"
						></b-form-datepicker
					></b-form-group>
					<b-form-group label="End Date" label-for="end_date-input">
						<b-form-datepicker
							id="end_date-input"
							v-model="end_date"
							placeholder="Choose End Date"
							class="mb-2"
						></b-form-datepicker>
					</b-form-group>
					<b-form-group label="Owner Name" label-for="owner-input">
						<b-form-input
							id="owner-input"
							v-model="owner"
							placeholder="Enter Owner Name ..."
						></b-form-input>
					</b-form-group>
				</form>
			</b-modal>
		</div>

		<b-form-input
			v-model="search"
			placeholder="Search by License Plate or Owner"
		></b-form-input>
		<LicensePlate v_if="plates.length" :plates="plates" :search="search" />
		{{ plateNumber }}{{ start_date }}{{ end_date }}{{ owner }}
	</div>
</template>

<script>
import LicensePlate from "./components/LicensePlate.vue";
import AppHeader from "./components/AppHeader.vue";

const BACKEND = "http://127.0.0.1:5001/api/v1/plate";

export default {
	name: "App",
	components: {
		AppHeader,
		LicensePlate
	},
	data() {
		return {
			plates: [],
			total: 0,
			search: "",
			plateNumber: "",
			start_date: "",
			end_date: "",
			owner: "",
			PlateState: null,
			dismissCountDown: 0,
			variant: "",
			message: ""
		};
	},
	methods: {
		countDownChanged(dismissCountDown) {
			this.dismissCountDown = dismissCountDown;
		},
		fetchData() {
			if (this.search)
				fetch(
					BACKEND + "?plate:like=" + this.search + "&owner_name:like=" + this.search,
					{
						method: "get"
					}
				)
					.then(response => {
						if (response.status === 200) return response.json();
					})
					.then(jsonData => {
						this.plates = jsonData.objects;
						this.total = jsonData.total_rows;
					});
			else
				fetch(BACKEND, {
					method: "get"
				})
					.then(response => {
						if (response.status === 200) return response.json();
					})
					.then(jsonData => {
						this.plates = jsonData.objects;
						this.total = jsonData.total_rows;
					});
		},
		checkFormValidity() {
			const valid = this.$refs.form.checkValidity();
			this.PlateState = valid;
			return valid;
		},
		resetModal() {
			(this.plateNumber = ""),
				(this.start_date = ""),
				(this.end_date = ""),
				(this.owner = "");
		},
		handleOk(bvModalEvent) {
			bvModalEvent.preventDefault();
			this.handleSubmit();
		},
		handleSubmit() {
			if (!this.checkFormValidity()) {
				return;
			}
			var body = { plate: this.plateNumber, owner_name: this.owner };
			if (this.start_date) {
				body["start_date"] = this.start_date + "T00:00:00.000Z";
			}

			if (this.end_date) {
				body["end_date"] = this.end_date + "T00:00:00.000Z";
			}
			const requestOptions = {
				method: "POST",
				headers: { "Content-Type": "application/json" },
				body: JSON.stringify(body)
			};
			fetch(BACKEND, requestOptions).then(response => {
				console.log(response);
				if (response.status === 201) {
					response.json();
					this.variant = "success";
					this.message = "Record Added.";
					this.dismissCountDown = 3;
					this.fetchData();
				} else {
					this.variant = "danger";
					this.message = "Invalid Request Sent.";
					this.dismissCountDown = 3;
				}
			});
			this.$nextTick(() => {
				this.$bvModal.hide("modal-prevent-closing");
			});
		}
	},
	watch: {
		search() {
			this.fetchData();
		}
	},
	mounted: function() {
		this.fetchData();
	}
};
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
