<template>
    <div id="carritob" style="margin: 20px 0;">
        <h2 id="titulo-tabla">Mis pedidos</h2>
        <table id="main-container">
            <thead>
                <tr>
                    <th>ID </th>
                    <th>Dirección</th>
                    <th>Fecha</th>
                    <th>Monto</th>
                    <th>Ver detalle</th>
                </tr>
            </thead>
            <tr v-for="(dataItem, index) in dataItemsLocal" :key="index">
                <td>
                    {{ dataItem.idPedido }}
                </td>
                <td>
                    {{ dataItem.direccion }}
                </td>
                <td>
                    {{ dataItem.fecha }}
                </td>
                <td>
                    {{ formatPrice(dataItem.monto) }}
                </td>
                <td>
                    <span v-b-modal.modal-prevent-closing-orders @click="viewDetails(dataItem.idPedido)"
                        style="cursor: pointer">
                        <b-icon-eye variant="info"></b-icon-eye>
                    </span>
                </td>
            </tr>
        </table>
        <b-modal id="modal-prevent-closing-orders" hide-footer :size="'xl'">
            <template #modal-title>
                Pedido {{ selected.idPedido }}
            </template>
            <div class="d-block text-center">
                <table id="main-container">
                    <thead>
                        <tr>
                            <th>
                                <b-icon-card-image></b-icon-card-image>
                            </th>
                            <th>Nombre </th>
                            <th>Tipo</th>
                            <th>Componentes </th>
                            <th>Cantidad</th>
                            <th>Precio por unidad</th>
                            <th>Precio Total</th>
                            <th>Acción</th>
                        </tr>
                    </thead>
                    <tr v-for="(dataItem, index) in dataItemsLocal" :key="index">
                        <td><img :src="'data:image/jpeg;base64,' + dataItem.picByte" class="imagen_carro" /></td>
                        <td>
                            {{ dataItem.nombre }}
                        </td>
                        <td v-if="dataItem.hasOwnProperty('tipo')">
                            {{ dataItem.tipo }}
                        </td>
                        <td v-else>
                            Menú
                        </td>
                        <td>
                            <b-list-group style="max-width: 400px; margin: 0 auto;">
                                <b-list-group-item v-for="(dataItem2, index2) in dataItem.componentes" :key="index2"
                                    class="d-flex justify-content-between align-items-center"
                                    style="background-color: inherit; border: none;">
                                    {{ dataItem2.nombre }}
                                    <b-badge v-if="dataItem.hasOwnProperty('tipo')" variant="primary" pill>{{
                                            dataItem2.cantidad
                                    }}{{ dataItem2.unidadMedida }}
                                    </b-badge>
                                    <b-badge v-else variant="primary" pill>{{ dataItem2.tipo }}
                                    </b-badge>
                                </b-list-group-item>
                            </b-list-group>
                        </td>
                        <td>
                            {{ dataItem.cantidad }}
                        </td>
                        <td>
                            {{ formatPrice(dataItem.precio) }}
                        </td>
                        <td>
                            {{ formatPrice(dataItem.precio * dataItem.cantidad) }}
                        </td>
                        <td>
                            <span @click="removeItemFromCart(dataItem.idProducto)" style="cursor: pointer">
                                <b-icon-x-circle-fill variant="danger"></b-icon-x-circle-fill>
                            </span>
                        </td>
                    </tr>
                </table>
                <br>
                <h2 class="monto">Monto Total: <b class="text-green">
                        {{ formatPrice(precioTotal) }}
                    </b></h2>
            </div>
            <b-button class="mt-3" block @click="$bvModal.hide('modal-prevent-closing-orders')">Close</b-button>
        </b-modal>
    </div>
</template>

<script>
import { BIcon, BIconEye, BIconCardImage } from 'bootstrap-vue';
import Pedido from '../model/Pedido';
export default {
    mounted() {
        this.onLoad();
    },
    data() {
        return {
            dataItemsLocal: [],
            direccion: '',
            selected: {}
        }
    },
    methods: {
        onLoad() {
            if (localStorage.getItem('token') != null) {
                this.$axios.defaults.headers.common['Authorization'] = 'Bearer ' + localStorage.getItem('token');
                if (localStorage.getItem('authorities') != 'Usuario') {
                    alert("Debe iniciar sesión como Usuario!");
                    this.$router.push('/log_in')
                }
                let url = 'http://localhost:8080/pedidos/getPedidos?id=' + localStorage.getItem('id');
                this.$axios.$get(url).then(res => {
                    this.dataItemsLocal = res;
                }).catch(err => {
                    console.log(err);
                });

            }

        },
        formatPrice(value) {
            let val = (value / 1).toFixed(2).replace('.', ',')
            return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
        },
        viewDetails(orderId) {
            this.selected = this.dataItemsLocal.find(o => o.idPedido == orderId);
        },
        handleSubmit() {
            var restaurants = [];
            let url = "http://localhost:8080/pedidos/newpedido?productos=";
            let productos = '';
            let menus = '';
            this.dataItemsLocal.forEach(element => {
                if (!restaurants.some(r => r.nit === element.restaurant.nit)) {
                    restaurants.push(element.restaurant);
                }
                console.log("Restaurantes:", restaurants)
                if (element.type_id == "producto") {
                    productos = productos.concat(element.idProducto, ",", element.cantidad, ",")
                }
                if (element.type_id == "menu") {
                    menus = menus.concat(element.idMenu, ",", element.cantidad, ",")
                }
            });
            productos = productos.substring(0, productos.length - 1);
            menus = menus.substring(0, menus.length - 1);

            url = url.concat(productos, "&menus=", menus);
            console.log(url);
            var dateString = new Date().toISOString().substring(0, 10);
            var pedido = new Pedido(this.precioTotal, this.direccion, dateString, Number(localStorage.getItem('id')), restaurants);
            console.log(pedido);
            this.$axios.$post(url, pedido).then(res => {
                console.log(res);
                this.$bvToast.toast('Pedido Realizado correctamente', {
                    title: 'Éxito',
                    variant: 'success',
                    solid: true
                });
            }).catch(err => {
                console.log(err);
            });
        }
    },
    components: {
        BIcon,
        BIconEye,
        BIconCardImage
    },
    computed: {
        precioTotal() {
            let precio = 0;
            this.dataItemsLocal.forEach(element => {
                precio += element.precio * element.cantidad;
            });
            return precio;

        }
    }
}

</script>