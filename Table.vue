<template>
	<div class="table--wrrrapper">
		<div
			:class="`table ${loading ? 'loading' : ''}`"
			:style="`min-height: ${height || '200px'}; max-height: ${
				height || '400px'
			}`"
		>
			<table>
				<thead>
					<tr>
						<th
							@click="`${item.sortable ? changeSort(item.column) : ''}`"
							v-for="(item, idx) in cols"
							:key="`col-${item.column}-${idx}`"
						>
							<div class="table-header">
								<span class="label">
									{{ item.label }}
								</span>
								<span
									v-if="item.sortable"
									style="margin-top: 6px"
									:class="[
										sortOrder === 'DESC' && sortCol === item.column
											? 'mdi mdi-chevron-down'
											: '',
										sortOrder === 'ASC' && sortCol === item.column
											? 'mdi mdi-chevron-up'
											: '',
										'mdi-24px',
									]"
								>
								</span>
							</div>
						</th>
					</tr>
				</thead>
				<tbody>
					<tr v-for="(row, rIdx) in sortedRows" :key="rIdx">
						<td
							v-for="(col, idx) in cols"
							:key="`col-${row[col.column]}-${idx}`"
							:style="`width: ${col.width}px;`"
						>
							<slot
								v-if="col.column == 'action'"
								name="action"
								:actions="row"
							></slot>

							<a
								class="link"
								v-else-if="col.column == 'email'"
								:href="`mailto:${row[col.column]}`"
								>{{ row[col.column] }}</a
							>
							<span v-else>{{
								col.column == "serial" ? rIdx + 1 : row[col.column]
							}}</span>
						</td>
					</tr>
				</tbody>
				<tfoot v-if="total">
					<tr>
						<td v-for="(item, idx) in cols" :key="`col-${item.column}-${idx}`">
							<span>
								{{
									item.sum ? sum(item.column) : idx == 1 ? "Total" : ""
								}}</span
							>
						</td>
					</tr>
				</tfoot>
			</table>
		</div>
	</div>
</template>
<script>
export default {
	props: {
		rows: {
			required: true,
			type: Array,
		},
		cols: {
			type: Array,
			required: true,
		},
		loading: {
			type: Boolean,
			default: false,
		},
		footer: {
			type: Boolean,
			default: false,
		},
		height: {
			type: String,
			default: "",
		},
		total: {
			type: Boolean,
			default: false,
		},
	},
	data() {
		return {
			sortCol: null,
			sortOrder: "DESC",
		};
	},
	mounted() {
		// set sort
		this.setSortCol();
	},
	methods: {
		setSortCol() {
			const col = this.cols.filter((col) => col.sort === true);
			if (col.length) {
				this.sortCol = col[0].column;
			}
		},
		sum(column) {
			let sum = 0;
			this.rows &&
				this.rows.map((row) => {
					const value = Number(row[column]);
					sum += value;
				});
			return sum.toLocaleString();
		},
		changeSort(column) {
			if (this.sortCol === column) {
				this.sortOrder = this.sortOrder === "ASC" ? "DESC" : "ASC";
			} else {
				this.sortCol = column;
				this.sortOrder = "DESC";
			}
		},
	},
	computed: {
		sortedRows() {
			if (this.sortCol === null) {
				return this.rows;
			}
			const sortOrder = this.sortOrder === "ASC" ? 1 : -1;
			const sortCol = this.sortCol;
			return (
				this.rows &&
				// eslint-disable-next-line vue/no-side-effects-in-computed-properties
				this.rows.sort((a, b) => {
					if (a[sortCol] < b[sortCol]) {
						return -1 * sortOrder;
						// a should come after b in the sorted order
					} else if (a[sortCol] > b[sortCol]) {
						return 1 * sortOrder;
						// and and b are the same
					} else {
						return 0;
					}
				})
			);
		},
	},
};
</script>
<style scoped>
.table--wrrrapper {
	border-radius: inherit;
	display: flex;
	flex-direction: column;
	justify-content: space-between;
}
.table {
	position: relative;
	overflow: auto;
	z-index: 1;
	background: var(--bg--color--primary);
	border-bottom: unset;
}
/* Fixed Headers */
th {
	position: -webkit-sticky;
	position: sticky;
	top: 0;
	z-index: 2;
}
th[scope="row"] {
	position: -webkit-sticky;
	position: sticky;
	left: 0;
	z-index: 1;
}
th[scope="row"] {
	vertical-align: top;
	color: inherit;
	background-color: inherit;
}
th:not([scope="row"]) {
	left: 0;
	z-index: 3;
}
.sort {
	display: inline-block;
	border-radius: 50px;
	position: relative;
	top: -1px;
}
.table.loading {
	overflow: hidden;
	height: 200px;
}
.table .loading {
	position: absolute;
	top: 0;
	left: 0;
	width: 100%;
	height: 100%;
	background-color: #303237;
	z-index: 2000;
	display: flex;
	justify-content: center;
	align-items: center;
	font-weight: 500;
	font-size: 20px;
}
.table table {
	width: 100%;
	position: relative;
	border-collapse: collapse;
}
.table tbody tr {
	border-bottom: 0.5px solid var(--border-color);
}
.table table thead tr th {
	top: -1px;
	position: -webkit-sticky;
	position: sticky;
	top: 0;
	z-index: 5;
	background: var(--bg--color--primary);
}
.table .table-header {
	display: flex;
	align-items: center;
}
.table table thead tr th .label {
	font-family: var(--font--family);
	font-style: normal;
	font-weight: bold;
	font-size: 14px;
	line-height: 20px;
	letter-spacing: 0.25px;
	color: var(--table-label-color);
}
.table table tr th {
	position: sticky;
	top: -1px;
	box-shadow: 0 0 2px -1px #303237;
	left: 0;
	margin: 0;
	padding: 0.6rem;
	font-size: 12px;
	cursor: pointer;
	text-align: left;
	white-space: nowrap;
}
.table table tr td {
	padding: 0.6rem;
}
.table table tr td:first-child {
	font-family: var(--font--family);
	font-style: normal;
	font-weight: bold;
	font-size: 14px;
	line-height: 20px;
	letter-spacing: 0.25px;
	color: var(--text-color);
}
.table table tr td span {
	font-family: var(--font--family);
	font-style: normal;
	font-weight: normal;
	font-size: 14px;
	line-height: 20px;
	letter-spacing: 0.25px;
  color: var(--text-color);
}
/* safari and ios need the tfoot itself to be position:sticky also */
.table table tfoot,
.table table tfoot th,
.table table tfoot td {
	position: -webkit-sticky;
	position: sticky;
	bottom: 0;
	z-index: 4;
}
.table table tfoot tr td {
	position: -webkit-sticky;
	position: sticky;
	z-index: 5;
	background: #303237;
}
.table table tfoot {
	border-top: 0.5px solid var(--border-color);
}
.table table tfoot tr td span {
	font-weight: 600;
}
.table--wrrrapper .footer {
	display: flex;
	justify-content: flex-end;
	margin: 10px 0;
}
</style>