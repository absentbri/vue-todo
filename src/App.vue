<template>
	<v-app id="inspire" v-cloak>
		<v-app-bar
			app
			color="indigo"
			dark
		>
			<v-toolbar-title>To-do List</v-toolbar-title>
		</v-app-bar>

		<v-content>
			<v-container fluid>

				<v-banner
					single-line
					v-show="isNotification"
				>
					<v-icon
						slot="icon"
						:color="notification.type"
						size="36"
					>
						mdi-alert-rhombus
					</v-icon>
					{{ notification.message }}

					<template v-slot:actions>
						<v-btn
							color="primary"
							text
							@click="clearNotification"
						>
							Close
						</v-btn>
					</template>
				</v-banner>

				<v-list
					two-line
					subheader
				>
					<v-container>

						<span><strong>{{todos.length}}</strong> Tasks left</span>

						<v-flex xs12>
							<v-text-field
								clearable
								v-model="newItem"
								label="Type your task"
								@keyup.enter="addItem"
							>
							</v-text-field>
						</v-flex>
					</v-container>

					<v-subheader class="subheading" v-if="!todos.length">You have no Tasks, add some</v-subheader>
					<v-subheader class="subheading" v-else>Your Tasks</v-subheader>

					<v-list-item
						v-for="(item, i) in todos"
						:key="i"
					>
						<template v-slot:default>
							<v-list-item-action>
								<v-checkbox
									v-model="item.completed"
									color="primary"
									@change="markItem($event, i)"
								></v-checkbox>
							</v-list-item-action>

							<v-list-item-content>
								<v-list-item-title
									:class="{completed: item.completed}"
								>
									{{ item.title }}
								</v-list-item-title>
								<v-list-item-subtitle>
									Created on: {{ formattedDate(item.created) }}
								</v-list-item-subtitle>
							</v-list-item-content>
							<v-btn
								icon
								ripple
								dark
								color="red"
								@click="removeItem(i)"
							>
								<v-icon>mdi-delete</v-icon>
							</v-btn>
						</template>
					</v-list-item>

				</v-list>
			</v-container>
		</v-content>

		<v-footer
			color="indigo"
			app
		>
			<span class="white--text">&copy; 2019</span>
		</v-footer>
	</v-app>
</template>

<script>
	import moment from 'moment'
	import axios from 'axios'

	export default {
		props: {
			endpoint: {
				type: String,
				default: "https://jsonplaceholder.typicode.com/todos"
			}
		},
		data: () => ({
			notification: {
				type: "",
				message: ""
			},
			newItem: "",
			todos: []
		}),
		computed: {
			isNotification() {
				return this.notification.message.length > 0;
			},
		},
		methods: {
			async addItem() {
				const title = this.newItem.trim();
				if(title.length) {
					const item = {
						title,
						created: moment().toDate(),
						completed: false
					};
					const res = await axios.post(this.endpoint, { data: item });
					console.log(res);
					if(res.status === 201) {
						item.id = res.data.id;
						this.todos.push(item);
					} else {
						this.setNotification("warning", `Could not create item, check the back-end service ${this.endpoint} is running`);
					}
				}
				this.newItem = "";
			},
			async removeItem(idx) {
				const { id } = this.todos[idx];
				const res = await axios.delete(`${this.endpoint}/${id}`);
				console.log(res);
				if(res.status === 200) { // should be 204?
					this.todos.splice(idx, 1);
				} else {
					this.setNotification("warning", `Could not delete item, check the back-end service ${this.endpoint} is running`);
				}
			},
			async markItem(event, idx) {
				const item = this.todos[idx];

				const res = await axios.put(`${this.endpoint}/${item.id}`, { data: item });
				if(res.status === 200) {
					// nothing?
				} else {
					this.setNotification("warning", `Could not update item, check the back-end service ${this.endpoint} is running`);
				}
			},
			formattedDate(date) {
				const dateObj = moment(date);
				return `${dateObj.format("dddd, D MMMM YYYY")} at ${dateObj.format("h:mm A")}`
			},
			setNotification(type, message) {
				this.notification.type = type || "";
				this.notification.message = message || "";
			},
			clearNotification() {
				this.notification.type = "";
				this.notification.message = "";
			},
			getItems() {
				// CRUD: Read
				axios.get(this.endpoint, { params: { _limit: 20 } })
					.then((response) => {
						if(response.status === 200) {
							this.todos = response.data;
							return true;
						}
						return false;
					})
					.catch((error) => {
						console.error(error);
					})
			},
		},
		created() {
			axios.defaults.headers.common['Accept'] = 'application/json';
			axios.defaults.headers.common['X-Requested-With'] = 'XMLHttpRequest';

			this.getItems();
		}
	}
</script>

<style>
	.completed {
		text-decoration: line-through;
	}
</style>
