<template>
	<div class="no-print center">
		<button class="no-print" onclick="window.print()">Enregister la facture</button>
		<button class="no-print" @click="addProduct()">Ajouter un produit</button>
		<button class="no-print" @click="removeProduct()">Supprimer un produit</button>
		<button class="no-print" @click="save()">Sauvegarder la configuration</button>
	</div>
	<div class="invoice" @drop.prevent="onDrop" @dragover.prevent="onDragOver">
		<div class="no-print fullscreen" v-if="dragOver" @dragleave.prevent="onDragLeave">
			<h1>Drop here</h1>
		</div>
		<div class="content">
			<div class="header">
				<div class="invoiceNumber">
					<h3>Facture n°{{ new Date().getFullYear() + '-' + invoiceNumber.toString() }}</h3>
					<input class="no-print" type="number" v-model="invoiceNumber" />
				</div>
				<h3>Date: {{ new Date().toLocaleDateString("fr-FR") }}</h3>
			</div>
			<div class="fromTo">
				<div class="from">
					<h3>De:</h3>
					<div class='info'>
						<p>{{ company.name }}</p><input type="text" class="no-print" v-model="company.name">
					</div>
					<div class='info'>
						<p>{{ company.address }}</p><input type="text" class="no-print" v-model="company.address">
					</div>
					<div class='info'>
						<p>{{ company.zip }}, {{ company.city }}</p>
						<input type="text" class="no-print" v-model="company.zip">
						<input type="text" class="no-print" v-model="company.city">
					</div>
					<div class='info'>
						<p>{{ company.country }}</p><input type="text" class="no-print" v-model="company.country">
					</div>
					<div class='info'>
						<p>{{ company.mail }}</p><input type="text" class="no-print" v-model="company.mail">
					</div>
				</div>
				<div class="to">
					<h3>À:</h3>
					<div class='info'>
						<p>{{ client.name }}</p><input type="text" class="no-print" v-model="client.name">
					</div>
					<div class='info'>
						<p>{{ client.address }}</p><input type="text" class="no-print" v-model="client.address">
					</div>
					<div class='info'>
						<p>{{ client.zip }}, {{ client.city }}</p>
						<input type="text" class="no-print" v-model="client.zip">
						<input type="text" class="no-print" v-model="client.city">
					</div>
					<div class='info'>
						<p>{{ client.country }}</p><input type="text" class="no-print" v-model="client.country">
					</div>
					<div class='info'>
						<p>{{ client.mail }}</p><input type="text" class="no-print" v-model="client.mail">
					</div>
				</div>
			</div>
			<div class="products">
				<table>
					<thead>
						<tr>
							<th>Désignation</th>
							<th>Date de fin</th>
							<th>Prix u. HT</th>
							<th>Qté</th>
							<th>Total HT</th>
						</tr>
					</thead>
					<tbody>
						<tr v-for="sale in sales">
							<div class='sale'>
								<td>{{ sale.name }}</td><input type="text" class="no-print smallInput"
									v-model="sale.name" />
							</div>
							<div class='sale'>
								<td>{{ sale.date }}</td><input type="text" class="no-print smallInput"
									v-model="sale.date" />
							</div>
							<div class='sale'>
								<td>{{ sale.price }} €</td><input type="number" class="no-print smallInput"
									v-model="sale.price" />
							</div>
							<div class='sale'>
								<td>{{ sale.quantity }}</td><input type="number" class="no-print smallInput"
									v-model="sale.quantity" />
							</div>
							<div class='sale'>
								<td>{{ sale.price * sale.quantity }} €</td>
							</div>
						</tr>
					</tbody>
					<tfoot>
						<tr style="font-weight: bold;">
							<td>TOTAL HT EN EUROS</td>
							<td>{{ sales.reduce((acc, sale) => acc + sale.price * sale.quantity, 0) }} €</td>
						</tr>
						<tr>
							<td style="width: 60%;">TVA non applicable, art. 293 B du CGI</td>
						</tr>
					</tfoot>
				</table>
			</div>
			<div class="payment">
				<h3>IBAN {{ company.name }}</h3>
				<div class="iban">
					<p>{{ company.iban }}</p>
					<input type="text" class="no-print" v-model="company.iban">
				</div>
				<p>La facture est payable sous 30 jours</p>
				<p>Escompte pour paiement anticipé: néant</p>
				<p>Tout règlement effectué après expiration du délai donnera lieu, à titre de pénalité de retard, à
					l'application d'un intérêt égal à celui appliqué par la Banque Centrale Européenne à son opération
					de refinancement la plus récente, majoré de 10 points de pourcentage, ainsi qu'à une indemnité
					forfaitaire pour frais de recouvrement d'un montant de 40 €.<br /> Les pénalités de retard sont
					exigibles sans qu'un rappel soit nécessaire.</p>
			</div>
			<div class="footer">
				<p>{{ company.name }} - {{ company.address }} - {{ company.zip }} - {{ company.country }}</p>
				<p>Siret: {{ company.siret }} - APE: {{ company.APE }}</p>
				<div class="siretAPEinput no-print">
					<input type="text" v-model="company.siret" />
					<input type="text" v-model="company.APE" />
				</div>

			</div>
		</div>
	</div>
</template>

<script lang="ts">

import { defineComponent } from 'vue';

export default defineComponent({
	data() {
		return {
			invoiceNumber: 1,
			company: {
				name: 'Acme Corp',
				address: '1234 Main Street',
				city: 'Springfield',
				country: 'France',
				zip: '62701',
				mail: 'mail@mail.mail',
				iban: 'FR76 3000 4000 5000 6000 7000 8000',
				siret: 'xxx xxx xxx xxxxx',
				APE: '0000X'
			},
			client: {
				name: 'Jane Doe',
				address: '1234 Main Street',
				city: 'Springfield',
				country: 'France',
				zip: '62701',
				mail: 'mail@mail.mail'
			},
			sales: [
				{
					name: 'Produit 1',
					date: '01/01/2021',
					price: 100,
					quantity: 2,
				},
				{
					name: 'Produit 2',
					date: '01/01/2021',
					price: 200,
					quantity: 1,
				},
				{
					name: 'Produit 3',
					date: '01/01/2021',
					price: 300,
					quantity: 1,
				},
			],
			dragOver: false,
		};
	},
	methods: {
		addProduct() {
			this.sales.push({
				name: '',
				date: '',
				price: 0,
				quantity: 0,
			});
		},
		removeProduct() {
			this.sales.pop();
		},
		// save in a file on the computer
		save() {
			const data = {
				invoiceNumber: this.invoiceNumber,
				company: this.company,
				client: this.client,
				sales: this.sales,
			};
			const a = document.createElement('a');
			const file = new Blob([JSON.stringify(data)], { type: 'application/json' });
			a.href = URL.createObjectURL(file);
			a.download = `invoice-${this.invoiceNumber}.json`;
			a.click();
		},
		async onDrop(event: DragEvent) {
			this.dragOver = false;
			if (event.dataTransfer) {
				const f = event.dataTransfer?.files[0];
				const data = JSON.parse(await f.text());
				this.invoiceNumber = data.invoiceNumber;
				this.company = data.company;
				this.client = data.client;
				this.sales = data.sales;
			}
		},
		onDragOver() {
			this.dragOver = true;
		},
		onDragLeave() {
			this.dragOver = false;
		},
		updateTitle() {
			document.title = 'Facture-' + new Date().getFullYear() + '-' + this.invoiceNumber;
		}
	},
	mounted() {
		this.updateTitle();
	},
	watch: {
		invoiceNumber() {
			this.updateTitle();
		}
	}
});

</script>
<style scoped>
.fullscreen {
	position: fixed;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: rgba(0, 0, 0, 0.5);
	color: white;
	display: flex;
	justify-content: center;
	align-items: center;
}

.footer {
	display: flex;
	flex-direction: column;
	align-self: center;
	text-align: center;

}

.footer p {
	margin: 0;
	font-weight: bold;
}

.iban {
	display: flex;
	flex-direction: column
}

.products {
	width: 100%;
}

.smallInput {
	width: 80%
}

.products table {
	display: flex;
	flex-direction: column;
	width: 100%;
	border: 1px solid black;
	padding: 10px;
}

.products table thead tr,
.products table tbody tr {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
	text-align: left;
	border-bottom: 1px solid black;
}

.products table thead th {
	width: 20%;
}

.products table tfoot tr {
	width: 100%;
	display: flex;
	flex-direction: row;
	justify-content: flex-end;
}

.products table tfoot td {
	width: 20%;
}

.products table tbody {
	display: flex;
	flex-direction: column;
}

.sale {
	display: flex;
	flex-direction: column;
	justify-content: space-between;
	width: 20%;
}

.fromTo {
	display: flex;
	flex-direction: row;
	justify-content: space-between;
}

.from,
.to {
	display: flex;
	flex-direction: column;
	max-width: 33.333%;
}

.info {
	display: flex;
	flex-direction: column;
}

.from p,
.to p {
	margin: 0px;
}



.center {
	display: flex;
	justify-content: center;
	width: 100%;
	justify-content: space-evenly;
	padding: 10px;
}

.center button {
	padding: 10px;
	font-size: 16px;
}

@media print {
	.no-print {
		display: none;
	}
}

.content {
	display: flex;
	flex-direction: column;
	width: 100%;
	height: calc(100% - 50px * 2);
	padding: 50px;
	justify-content: space-between;

}

.invoice {
	display: flex;
	flex-direction: row;
	width: 210mm;
	height: 297mm;
	border: 1px solid black;
}

.header {
	display: flex;
	flex-direction: column;
	width: 100%;
}

.invoiceNumber {
	display: flex;
	flex-direction: row;
	justify-content: flex-start;
}

h3 {
	margin-bottom: 5px;
}

.invoiceNumber input {
	margin-left: 10px;
	width: 50px;
}
</style>
