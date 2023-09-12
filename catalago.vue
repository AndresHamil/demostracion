<template>
    <div v-if="user != null && currentProcess != null">
        <!-- nav -->
        <nav class="azt-nav">
            <!-- nav -> title -->
            <template v-if="currentModule != null">
                <h3>{{ currentModule.moduloNombre }}</h3>
            </template>
            <template v-else>
                <h3>
                    <el-skeleton-item variant="h3" style="width: 160px;" />
                </h3>
            </template>
            <!-- nav -> menu -->
            <template v-if="currentProcess != null">
                <el-menu mode="horizontal">
                    <template v-for="(process, index) in accessProcesses">
                        <template v-if="process.fkModuloId == currentModule.id">
                            <el-menu-item
                                :style="$route.fullPath == process.procesoUrl || $route.fullPath == (process.procesoUrl + '/') ? 'border-bottom: 2px solid #1E294A !important; color: #444444 !important;' : 'border-bottom: 2px solid transparent !important;'"
                                :key="index">
                                <nuxt-link :to="process.procesoUrl"> {{ process.procesoNombre }} </nuxt-link>
                            </el-menu-item>
                        </template>
                    </template>
                </el-menu>
            </template>
            <template v-else>
                <el-menu mode="horizontal">
                    <el-menu-item>
                        <el-skeleton-item variant="h3" style="width: 80px;" />
                    </el-menu-item>
                    <el-menu-item>
                        <el-skeleton-item variant="h3" style="width: 100px;" />
                    </el-menu-item>
                </el-menu>
            </template>
        </nav>

        <template v-if="currentProcess.procesoStatus">
            <div class="azt-table" data-moptions="false">
                <!----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
                <!-- - CONFIGURACIONES  -->
                <div class="azt-table__box">
                    <!-- OPCIONES -->
                    <div class="azt-table__pagination">
                        <template v-if="configuraciones.table.data != null">
                            <section>
                                <b class="title-tabla">Configuraciones</b>
                                <b>Elementos por pagina: </b>
                                <el-select v-model="configuraciones.table.filtros.pagination.visibleRecords">
                                    <el-option v-for="item in configuraciones.table.pagination.visibleRecordOptions" :key="item.value"
                                        :label="item.label" :value="item.value">
                                    </el-option>
                                </el-select>

                                <b class="azt-table__pagination-pages">Pagina: </b>
                                <el-select v-model="configuraciones.table.filtros.pagination.currentPage">
                                    <el-option v-for="item in configuraciones.table.pagination.pages" :key="item.value" :label="item.label"
                                        :value="item.value">
                                    </el-option>
                                </el-select>
                                <b> de {{ configuraciones.table.pagination.pages.length }} </b>
                                <span>( Total: <b> {{ configuraciones.table.pagination.records }} </b> Registros )</span>
                            </section>
                            <ul class="azt-body__menu-options">
                                <li title="Recargar información" @click="actualizarTablaFiltrosConfiguraciones()"><i
                                        :class="configuraciones.table.loading ? 'fas fa-sync rotating' : 'fas fa-sync'"></i></li>
                                <li :title="configuraciones.table.filtros.ordenarPor == 'ultimos_registrados' ? 'Ordenar por primeros registros' : 'Ordenar por ultimos registros'" @click="configuraciones.table.filtros.ordenarPor == 'ultimos_registrados' ? configuraciones.table.filtros.ordenarPor = 'primeros_registrados' :  configuraciones.table.filtros.ordenarPor = 'ultimos_registrados'">
                                    <i :class="configuraciones.table.filtros.ordenarPor == 'ultimos_registrados' ? 'fas fa-sort-amount-up' : 'fas fa-sort-amount-down-alt'"></i>
                                </li>
                            </ul>
                        </template>
                        <template v-else>
                            <section>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                            </section>
                            <span>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                                <el-skeleton-item variant="h3" style="width: 20px;" />
                                <el-skeleton-item variant="h3" style="width: 60px;" />
                            </span>
                        </template>
                    </div>
                    <!-- TABLA -->
                    <div class="azt-table__data">
                        <template v-if="configuraciones.table.data == null">
                            <span style="opacity: 0.5;"><i class="el-icon-loading" style="margin-right: 5px;"></i> Cargando
                                información...</span>
                        </template>
                        <template v-else>
                            <el-table
                                :data="configuraciones.table.data"
                                style="width: 100%"
                                :v-loading="configuraciones.table.loading"
                                element-loading-text="Loading..."
                                element-loading-spinner="el-icon-loading"
                                element-loading-background="rgba(0, 0, 0, 0.8)"
                                height="100%">
                               
                                <el-table-column prop="" label="Id" width="120" fixed>
                                    <template slot="header" slot-scope="scope">
                                        Id
                                        <el-input v-model="configuraciones.table.filtros.id" size="mini" placeholder="Buscar..."
                                            maxlength="3"
                                            onkeypress="return event.charCode>=48 && event.charCode<=57" clearable />
                                    </template>
                                    <template slot-scope="scope">
                                        {{ scope.row.id }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" width="200">
                                    <template slot="header" slot-scope="scope">
                                        Nombre
                                        <el-input v-model="configuraciones.table.filtros.nombre" size="mini" placeholder="Buscar..."
                                        maxlength="30"
                                        onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" clearable />
                                    </template>
                                    <template slot-scope="scope">
                                        {{ scope.row.nombre }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Descripcion" width="600">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.descripcion != null">
                                            {{ scope.row.descripcion }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin descripcion</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" width="150">
                                    <template slot="header" slot-scope="scope">
                                        Tipo
                                        <el-input v-model="configuraciones.table.filtros.tipo" size="mini" placeholder="Buscar..."
                                        maxlength="30"
                                        onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" clearable />
                                    </template>
                                    <template slot-scope="scope">
                                        {{ scope.row.tipo }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Primer valor" min-width="150">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center" v-if="scope.row.valor1 != null">
                                            {{ scope.row.valor1 }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin asingar</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Segundo valor" min-width="150">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center" v-if="scope.row.valor2 != null">
                                            {{ scope.row.valor2 }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin asingar</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Fecha Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.fechaRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Usuario Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.usuarioRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="sisStatus" label="Estado" width="145">    
                                    <template slot="header" slot-scope="scope">
                                        Estado
                                        <el-select v-model="configuraciones.table.filtros.sisStatus" placeholder="Select"
                                            size="mini">
                                            <el-option v-for="item in configuraciones.table.filtros.estadoOptions" :key="item.value"
                                                :label="item.label" :value="item.value">
                                            </el-option>
                                        </el-select>
                                    </template>  
                                    <template slot-scope="scope">
                                        <div class="azt-table__center">
                                            <el-switch v-if="!user.tipo"
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                @change="cambiarEstadoConfiguracion(scope.row)"
                                                >
                                            </el-switch>

                                            <el-switch v-else
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                disabled
                                                >
                                            </el-switch>
                                        </div> 
                                    </template>
                                </el-table-column>

                                <el-table-column fixed="right" label="Acciones" width="130" v-if="currentProcess.tipo || !user.tipo">
                                    <template slot-scope="scope">
                                        <el-dropdown size="small" @command="opcionesTableConfiguraciones"
                                            v-if="currentProcess.tipo || !user.tipo">
                                            <el-button type="primary" size="small" style="background:#16A596">
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                            <el-dropdown-menu slot="dropdown">
                                                <el-dropdown-item :command="{ action: 'editar', data: scope.row }">Editar</el-dropdown-item>
                                                <el-dropdown-item :command="{ action: 'eliminar', data: scope.row }">Eliminar</el-dropdown-item>
                                            </el-dropdown-menu>
                                        </el-dropdown>
                                        <el-tooltip placement="top" v-else>
                                            <div slot="content">No tienes los permisos
                                                <br>
                                                necesarios para realizar
                                                <br>
                                                estas acciones
                                            </div>
                                            <el-button type="primary" size="small" style="background:#16A596" disabled>
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                        </el-tooltip>
                                    </template>
                                </el-table-column>

                            </el-table>
                        </template>
                    </div>
                    <!-- ADD -->
                    <div class="azt-table__box__section_icon"
                        @click="configuraciones.formulario.visivilidad.formulario = true;" v-if="currentProcess.tipo || user.tipo == 0">
                        <i class="far fa-plus"></i>
                    </div>
                </div> 
                <!----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
                <!-- - CLASIFICACIONES  -->
                <div class="azt-table__box">
                    <!-- OPCIONES -->
                    <div class="azt-table__pagination">
                        <template v-if="clasificaciones.table.data != null">
                            <section>
                                <b class="title-tabla">Clasificaciones </b>
                                <b>Elementos por pagina: </b>
                                <el-select v-model="clasificaciones.table.filtros.pagination.visibleRecords">
                                    <el-option v-for="item in clasificaciones.table.pagination.visibleRecordOptions" :key="item.value"
                                        :label="item.label" :value="item.value">
                                    </el-option>
                                </el-select>

                                <b class="azt-table__pagination-pages">Pagina: </b>
                                <el-select v-model="clasificaciones.table.filtros.pagination.currentPage">
                                    <el-option v-for="item in clasificaciones.table.pagination.pages" :key="item.value" :label="item.label"
                                        :value="item.value">
                                    </el-option>
                                </el-select>
                                <b> de {{ clasificaciones.table.pagination.pages.length }} </b>
                                <span>( Total: <b> {{ clasificaciones.table.pagination.records }} </b> Registros )</span>
                            </section>
                            <ul class="azt-body__menu-options">
                                <li title="Recargar información" @click="actualizarTablaFiltrosClasificaciones()"><i
                                        :class="clasificaciones.table.loading ? 'fas fa-sync rotating' : 'fas fa-sync'"></i></li>
                                <li :title="clasificaciones.table.filtros.ordenarPor == 'ultimos_registrados' ? 'Ordenar por primeros registros' : 'Ordenar por ultimos registros'" @click="clasificaciones.table.filtros.ordenarPor == 'ultimos_registrados' ? clasificaciones.table.filtros.ordenarPor = 'primeros_registrados' :  clasificaciones.table.filtros.ordenarPor = 'ultimos_registrados'">
                                    <i :class="clasificaciones.table.filtros.ordenarPor == 'ultimos_registrados' ? 'fas fa-sort-amount-up' : 'fas fa-sort-amount-down-alt'"></i>
                                </li>
                            </ul>
                        </template>
                        <template v-else>
                            <section>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                            </section>
                            <span>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                                <el-skeleton-item variant="h3" style="width: 20px;" />
                                <el-skeleton-item variant="h3" style="width: 60px;" />
                            </span>
                        </template>
                    </div>
                    <!-- TABLA -->
                    <div class="azt-table__data">
                        <template v-if="clasificaciones.table.data == null">
                            <span style="opacity: 0.5;"><i class="el-icon-loading" style="margin-right: 5px;"></i> Cargando
                                información...</span>
                        </template>
                        <template v-else>
                            <el-table
                                :data="clasificaciones.table.data"
                                style="width: 100%"
                                :v-loading="clasificaciones.table.loading"
                                element-loading-text="Loading..."
                                element-loading-spinner="el-icon-loading"
                                element-loading-background="rgba(0, 0, 0, 0.8)"
                                height="100%">
                               
                                <el-table-column prop="" label="Id" width="120" fixed>
                                    <template slot="header" slot-scope="scope">
                                        Id
                                        <el-input v-model="clasificaciones.table.filtros.id" size="mini" placeholder="Buscar..."
                                            onkeypress="return event.charCode>=48 && event.charCode<=57" clearable />
                                    </template>
                                    <template slot-scope="scope">
                                        {{ scope.row.id }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" width="200">
                                    <template slot="header" slot-scope="scope">
                                        Nombre
                                        <el-input v-model="clasificaciones.table.filtros.nombre" size="mini" placeholder="Buscar..."
                                        onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" clearable />
                                    </template>
                                    <template slot-scope="scope">
                                        {{ scope.row.nombre }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Descripcion" width="600">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.descripcion != null">
                                            {{ scope.row.descripcion }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin descripcion</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Tipo" min-width="150">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{ scope.row.tipo}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Valor" min-width="150">
                                    <template slot-scope="scope">
                                        <!-- <p class="azt-text--center">
                                            {{ scope.row.valor}}
                                        </p> -->

                                        <p class="azt-text--center" v-if="scope.row.valor != null">
                                            {{ scope.row.valor }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin asignar</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Fecha Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.fechaRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Usuario Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.usuarioRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="sisStatus" label="Estado" width="145">                                      
                                    <template slot-scope="scope">
                                        <div class="azt-table__center">
                                            <el-switch v-if="!user.tipo"
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                @change="cambiarEstadoClasificacion(scope.row)"
                                                >
                                            </el-switch>

                                            <el-switch v-else
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                disabled
                                                >
                                            </el-switch>
                                        </div> 
                                    </template>
                                </el-table-column>

                                <el-table-column fixed="right" label="Acciones" width="130" v-if="currentProcess.tipo || !user.tipo">
                                    <template slot-scope="scope">
                                        <el-dropdown size="small" @command="opcionesTableClasificaciones"
                                            v-if="currentProcess.tipo || !user.tipo">
                                            <el-button type="primary" size="small" style="background:#16A596">
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                            <el-dropdown-menu slot="dropdown">
                                                <el-dropdown-item :command="{ action: 'editar', data: scope.row }">Editar</el-dropdown-item>
                                                <el-dropdown-item :command="{ action: 'eliminar', data: scope.row }">Eliminar</el-dropdown-item>
                                            </el-dropdown-menu>
                                        </el-dropdown>
                                        <el-tooltip placement="top" v-else>
                                            <div slot="content">No tienes los permisos
                                                <br>
                                                necesarios para realizar
                                                <br>
                                                estas acciones
                                            </div>
                                            <el-button type="primary" size="small" style="background:#16A596" disabled>
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                        </el-tooltip>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </template>
                    </div>
                    <!-- ADD -->
                    <div class="azt-table__box__section_icon"
                        @click="clasificaciones.formulario.visivilidad.formulario = true;" v-if="currentProcess.tipo || user.tipo == 0">
                        <i class="far fa-plus"></i>
                    </div>
                </div>  
                <!----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
                <!-- - MONEDA  -->
                <div class="azt-table__box">
                    <!-- OPCIONES -->
                    <div class="azt-table__pagination">
                        <template v-if="monedas.table.data != null">
                            <section>
                                <b class="title-tabla">Monedas </b>
                                <span>( Total: <b> {{ monedas.table.pagination.records }} </b> Registros )</span>
                            </section>
                            <ul class="azt-body__menu-options">
                                <li title="Recargar información" @click="consultarMonedas()">
                                    <i :class="monedas.table.loading ? 'fas fa-sync rotating' : 'fas fa-sync'"></i>
                                </li>
                            </ul>
                        </template>
                        <template v-else>
                            <section>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                            </section>
                            <span>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                                <el-skeleton-item variant="h3" style="width: 20px;" />
                                <el-skeleton-item variant="h3" style="width: 60px;" />
                            </span>
                        </template>
                    </div>
                    <!-- TABLA -->
                    <div class="azt-table__data">
                        <template v-if="monedas.table.data == null">
                            <span style="opacity: 0.5;"><i class="el-icon-loading" style="margin-right: 5px;"></i> Cargando
                                información...</span>
                        </template>
                        <template v-else>
                            <el-table
                                :data="monedas.table.data"
                                style="width: 100%"
                                :v-loading="monedas.table.loading"
                                element-loading-text="Loading..."
                                element-loading-spinner="el-icon-loading"
                                element-loading-background="rgba(0, 0, 0, 0.8)"
                                height="100%">
                               
                                <el-table-column prop="" label="Id" width="120" fixed>
                                    <template slot-scope="scope">
                                        {{ scope.row.id }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Clave" width="200">
                                    <template slot-scope="scope">
                                        {{ scope.row.clave }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Descripcion" width="600">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.descripcion">
                                            {{ scope.row.descripcion }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin descripcion</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Fecha Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.fechaRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Usuario Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.usuarioRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="sisStatus" label="Estado" width="145">                                      
                                    <template slot-scope="scope">
                                        <div class="azt-table__center">
                                            <el-switch v-if="!user.tipo"
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                @change="cambiarEstadoMoneda(scope.row)"
                                                >
                                            </el-switch>

                                            <el-switch v-else
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                disabled
                                                >
                                            </el-switch>
                                        </div> 
                                    </template>
                                </el-table-column>

                                <el-table-column fixed="right" label="Acciones" width="130" v-if="currentProcess.tipo || !user.tipo">
                                    <template slot-scope="scope">
                                        <el-dropdown size="small" @command="opcionesTableMonedas"
                                            v-if="currentProcess.tipo || !user.tipo">
                                            <el-button type="primary" size="small" style="background:#16A596">
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                            <el-dropdown-menu slot="dropdown">
                                                <el-dropdown-item :command="{ action: 'editar', data: scope.row }">Editar</el-dropdown-item>
                                                <el-dropdown-item :command="{ action: 'eliminar', data: scope.row }">Eliminar</el-dropdown-item>
                                            </el-dropdown-menu>
                                        </el-dropdown>
                                        <el-tooltip placement="top" v-else>
                                            <div slot="content">No tienes los permisos
                                                <br>
                                                necesarios para realizar
                                                <br>
                                                estas acciones
                                            </div>
                                            <el-button type="primary" size="small" style="background:#16A596" disabled>
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                        </el-tooltip>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </template>
                    </div>
                    <!-- ADD -->
                    <div class="azt-table__box__section_icon"
                        @click="monedas.formulario.visivilidad.formulario = true;" v-if="currentProcess.tipo || user.tipo == 0">
                        <i class="far fa-plus"></i>
                    </div>
                </div>
                <!----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
                <!-- - ESTATUS  -->
                <div class="azt-table__box">
                    <!-- PAGINACION -->
                    <div class="azt-table__pagination">
                        <template v-if="status.table.data != null">
                            <section>
                                <b class="title-tabla">Status </b>
                                <span>( Total: <b> {{ status.table.pagination.records }} </b> Registros )</span>
                            </section>
                            <ul class="azt-body__menu-options">
                                <li title="Recargar información" @click="consultarStatus()">
                                    <i :class="status.table.loading ? 'fas fa-sync rotating' : 'fas fa-sync'"></i>
                                </li>
                            </ul>
                        </template>
                        <template v-else>
                            <section>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                            </section>
                            <span>
                                <el-skeleton-item variant="h3" style="width: 80px;" />
                                <el-skeleton-item variant="h3" style="width: 20px;" />
                                <el-skeleton-item variant="h3" style="width: 60px;" />
                            </span>
                        </template>
                    </div>
                    <!-- TABLA -->
                    <div class="azt-table__data">
                        <template v-if="status.table.data == null">
                            <span style="opacity: 0.5;"><i class="el-icon-loading" style="margin-right: 5px;"></i> Cargando
                                información...</span>
                        </template>
                        <template v-else>
                            <el-table
                                :data="status.table.data"
                                style="width: 100%"
                                :v-loading="status.table.loading"
                                element-loading-text="Loading..."
                                element-loading-spinner="el-icon-loading"
                                element-loading-background="rgba(0, 0, 0, 0.8)"
                                height="100%">
                               
                                <el-table-column prop="" label="Id" width="120" fixed>
                                    <template slot-scope="scope">
                                        {{ scope.row.id }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Nombre" width="200">
                                    <template slot-scope="scope">
                                        {{ scope.row.nombre }}
                                    </template>
                                </el-table-column>


                                <el-table-column prop="" label="Tipo" width="200">
                                    <template slot-scope="scope">
                                        {{ scope.row.tipo }}
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Descripcion" width="600">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.descripcion">
                                            {{ scope.row.descripcion }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin descripcion</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Primer valor extra" width="200">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.valorExtra">
                                            {{ scope.row.valorExtra }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin primer valor extra</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Segundo valor extra" width="200">
                                    <template slot-scope="scope">
                                        <p v-if="scope.row.valorExtra2">
                                            {{ scope.row.valorExtra2 }}
                                        </p>
                                        <span class="azt-table__no-data" v-else>Sin segundo valor extra</span>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Ordenamiento" width="120">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.ordenamiento}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Fecha Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.fechaRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="" label="Usuario Registro" min-width="180">
                                    <template slot-scope="scope">
                                        <p class="azt-text--center">
                                            {{scope.row.usuarioRegistro}}
                                        </p>
                                    </template>
                                </el-table-column>

                                <el-table-column prop="sisStatus" label="Estado" width="145">                                      
                                    <template slot-scope="scope">
                                        <div class="azt-table__center">
                                            <el-switch v-if="!user.tipo"
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                @change="cambiarEstadoStatu(scope.row)"
                                                >
                                            </el-switch>

                                            <el-switch v-else
                                                v-model="scope.row.sisStatus"
                                                active-color="#13ce66"
                                                inactive-color="#ff4949"
                                                disabled
                                                >
                                            </el-switch>
                                        </div> 
                                    </template>
                                </el-table-column>

                                <el-table-column fixed="right" label="Acciones" width="130" v-if="currentProcess.tipo || !user.tipo">
                                    <template slot-scope="scope">
                                        <el-dropdown size="small" @command="opcionesTableStatus"
                                            v-if="currentProcess.tipo || !user.tipo">
                                            <el-button type="primary" size="small" style="background:#16A596">
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                            <el-dropdown-menu slot="dropdown">
                                                <el-dropdown-item :command="{ action: 'editar', data: scope.row }">Editar</el-dropdown-item>
                                                <el-dropdown-item :command="{ action: 'eliminar', data: scope.row }">Eliminar</el-dropdown-item>
                                            </el-dropdown-menu>
                                        </el-dropdown>
                                        <el-tooltip placement="top" v-else>
                                            <div slot="content">No tienes los permisos
                                                <br>
                                                necesarios para realizar
                                                <br>
                                                estas acciones
                                            </div>
                                            <el-button type="primary" size="small" style="background:#16A596" disabled>
                                                Acciones<i class="el-icon-arrow-down el-icon--right"></i>
                                            </el-button>
                                        </el-tooltip>
                                    </template>
                                </el-table-column>
                            </el-table>
                        </template>
                    </div>
                    <!-- ADD -->
                    <div class="azt-table__box__section_icon"
                        @click="status.formulario.visivilidad.formulario = true;" v-if="currentProcess.tipo || user.tipo == 0">
                        <i class="far fa-plus"></i>
                    </div>
                </div>
                <!----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
            </div>
            <!--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
            <!-- - FORMULARIO CONFIGURACIONES  -->
            <el-dialog
                :title="(!configuraciones.formulario.data.id ? 'Registrar' : 'Editar') + ' configuracion' "
                :visible.sync="configuraciones.formulario.visivilidad.formulario" :close-on-press-escape="true" :close-on-click-modal="false"
                :append-to-body="true" :destroy-on-close="true"
                @close="limpiarDataConfiguraciones()">
                <div class="azt-form__content" v-if="!configuraciones.formulario.data">
                    <template>
                        <span style="opacity: 0.5;">
                            <i class="el-icon-loading" style="margin-right: 5px;"></i>
                            Cargando información...
                        </span>
                    </template>
                </div>
                <el-form v-else :model="configuraciones.formulario.data" :rules="configuraciones.formulario.validaciones" ref="form" label-position="top" status-icon>
                    <!----------------------------------------------------------------------[INPUTS] -->
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Nombre" 
                                prop="nombre">
                                <el-input 
                                    v-model="configuraciones.formulario.data.nombre"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" 
                                    maxlength="50"
                                    @input="handleInputConfiguracionNombre"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                        <el-col :span="12">
                            <el-form-item 
                                label="Tipo" 
                                prop="tipo">
                                <el-input 
                                    v-model="configuraciones.formulario.data.tipo"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32"
                                    maxlength="30"
                                    @input="handleInputConfiguracionTipo"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                    </el-row>
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Primer valor extra">
                                <el-input 
                                    v-model="configuraciones.formulario.data.primerValor"
                                    maxlength="30"
                                    @input="handleInputConfiguracionPrimerValor"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>   

                        <el-col :span="12">
                            <el-form-item 
                                label="Segundo valor extra">
                                <el-input 
                                    v-model="configuraciones.formulario.data.segundoValor"
                                    maxlength="30"
                                    @input="handleInputConfiguracionSegundoValor"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>  



                    </el-row>

                    <div class="azt-form__separator"></div>
                    <el-row :gutter="24">
                        <el-col :span="24">
                            <el-form-item label="Descripcion:">
                                <el-input
                                type="textarea"
                                placeholder=""
                                v-model="configuraciones.formulario.data.descripcion"
                                @input="handleInputConfiguracionDescripcion"
                                maxlength="100"
                                show-word-limit>
                                </el-input>
                            </el-form-item>
                        </el-col>   
                    </el-row>
                    <br>
                    <!----------------------------------------------------------------------[BOTONES] -->
                    <el-form-item class="azt-form__btn">
                        <el-button 
                            v-if="!configuraciones.formulario.data.id" 
                            type="success" 
                            :loading="configuraciones.formulario.visivilidad.loadingRegistrar" 
                            :disabled="!configuraciones.formulario.visivilidad.registrar"
                            @click="validarFormularioConfiguraciones('registrar')">
                            Registrar
                        </el-button>
                        <el-button 
                            v-if="configuraciones.formulario.data.id" 
                            type="success"
                            :loading="configuraciones.formulario.visivilidad.loadingGuardar"
                            :disabled="!configuraciones.formulario.visivilidad.guardar"   
                            @click="validarFormularioConfiguraciones('editar')">
                            Guardar
                        </el-button>
                    </el-form-item>
                </el-form>
            </el-dialog>
            <!-- - FORMULARIO CLASIFICACIONES  -->
            <el-dialog
                :title="(!clasificaciones.formulario.data.id ? 'Registrar' : 'Editar') + ' clasificacion' "
                :visible.sync="clasificaciones.formulario.visivilidad.formulario" :close-on-press-escape="true" :close-on-click-modal="false"
                :append-to-body="true" :destroy-on-close="true"
                @close="limpiarDataClasificaciones()">
                <div class="azt-form__content" v-if="!clasificaciones.formulario.data">
                    <template>
                        <span style="opacity: 0.5;">
                            <i class="el-icon-loading" style="margin-right: 5px;"></i>
                            Cargando información...
                        </span>
                    </template>
                </div>
                <el-form v-else :model="clasificaciones.formulario.data" :rules="clasificaciones.formulario.validaciones" ref="form" label-position="top" status-icon>
                    <!----------------------------------------------------------------------[INPUTS] -->
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Nombre" 
                                prop="nombre">
                                <el-input 
                                    v-model="clasificaciones.formulario.data.nombre"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" 
                                    maxlength="100"
                                    @input="handleInputClasificacionesNombre"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                        <el-col :span="12">
                            <el-form-item 
                                label="Tipo" 
                                prop="tipo">
                                <el-input 
                                    v-model="clasificaciones.formulario.data.tipo"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32"
                                    maxlength="100"
                                    @input="handleInputClasificacionesTipo"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                    </el-row>
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Valor" 
                                prop="valor">
                                <el-input 
                                    v-model="clasificaciones.formulario.data.valor"
                                    onkeypress="return (event.charCode >= 48 && event.charCode <= 57) || (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122)"
                                    maxlength="7"
                                    @input="handleInputClasificacionesValor"
                                    oninput="this.value = this.value.toUpperCase()" 
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>   


                    </el-row>
                    <div class="azt-form__separator"></div>
                    <el-row :gutter="24">
                        <el-col :span="24">
                            <el-form-item label="Descripcion:">
                                <el-input
                                type="textarea"
                                placeholder=""
                                v-model="clasificaciones.formulario.data.descripcion"
                                @input="handleInputClasificacionesDescripcion"
                                maxlength="100"
                                show-word-limit>
                                </el-input>
                            </el-form-item>
                        </el-col>   
                    </el-row>
                    <br>
                    <!----------------------------------------------------------------------[BOTONES] -->
                    <el-form-item class="azt-form__btn">
                        <el-button 
                            v-if="!clasificaciones.formulario.data.id" 
                            type="success" 
                            :loading="clasificaciones.formulario.visivilidad.loadingRegistrar"  
                            :disabled="!clasificaciones.formulario.visivilidad.registrar"
                            @click="validarFormularioclasificaciones('registrar')">
                            Registrar
                        </el-button>
                        <el-button 
                            v-if="clasificaciones.formulario.data.id" 
                            type="success" 
                            :loading="clasificaciones.formulario.visivilidad.loadingGuardar"  
                            :disabled="!clasificaciones.formulario.visivilidad.guardar"
                            @click="validarFormularioclasificaciones('editar')">
                            Guardar
                        </el-button>
                    </el-form-item>
                </el-form>
            </el-dialog>
            <!-- - FORMULARIO MONEDAS  -->
            <el-dialog
                :title="(!monedas.formulario.data.id ? 'Registrar' : 'Editar') + ' moneda' "
                :visible.sync="monedas.formulario.visivilidad.formulario" :close-on-press-escape="true" :close-on-click-modal="false"
                :append-to-body="true" :destroy-on-close="true"
                @close="limpiarDataMonedas()">
                <div class="azt-form__content" v-if="!monedas.formulario.data">
                    <template>
                        <span style="opacity: 0.5;">
                            <i class="el-icon-loading" style="margin-right: 5px;"></i>
                            Cargando información...
                        </span>
                    </template>
                </div>
                <el-form v-else :model="monedas.formulario.data" :rules="monedas.formulario.validaciones" ref="form" label-position="top" status-icon>
                    <!----------------------------------------------------------------------[INPUTS] -->
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Clave" 
                                prop="clave">
                                <el-input 
                                    v-model="monedas.formulario.data.clave"
                                    @input="handleInputClave"
                                    oninput="this.value = this.value.toUpperCase()" 
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122)"
                                    maxlength="3"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>


                    </el-row>

                    <div class="azt-form__separator"></div>
                    <el-row :gutter="24">
                        <el-col :span="24">
                            <el-form-item label="Descripcion:">
                                <el-input
                                type="textarea"
                                placeholder=""
                                @input="handleInputDescripcion"
                                v-model="monedas.formulario.data.descripcion"
                                maxlength="100"
                                show-word-limit>
                                </el-input>
                            </el-form-item>
                        </el-col>   
                    </el-row>
                    <br>
                    <!----------------------------------------------------------------------[BOTONES] -->
                    <el-form-item class="azt-form__btn">
                        <el-button 
                            v-if="!monedas.formulario.data.id" 
                            type="success" 
                            :loading="monedas.formulario.visivilidad.loadingRegistrar"  
                            :disabled="!monedas.formulario.visivilidad.registrar"
                            @click="validarFormularioMonedas('registrar')">
                            Registrar
                        </el-button>
                        <el-button 
                            v-if="monedas.formulario.data.id" 
                            type="success"
                            :loading="monedas.formulario.visivilidad.loadingGuardar"  
                            :disabled="!monedas.formulario.visivilidad.guardar"
                            @click="validarFormularioMonedas('editar')">
                            Guardar
                        </el-button>
                    </el-form-item>
                </el-form>
            </el-dialog>
            <!-- - FORMULARIO STATUS  -->
            <el-dialog
                :title="(!status.formulario.data.id ? 'Registrar' : 'Editar') + ' status' "
                :visible.sync="status.formulario.visivilidad.formulario" :close-on-press-escape="true" :close-on-click-modal="false"
                :append-to-body="true" :destroy-on-close="true"
                @close="limpiarDataStatus()">
                <div class="azt-form__content" v-if="!status.formulario.data">
                    <template>
                        <span style="opacity: 0.5;">
                            <i class="el-icon-loading" style="margin-right: 5px;"></i>
                            Cargando información...
                        </span>
                    </template>
                </div>
                <el-form v-else :model="status.formulario.data" :rules="status.formulario.validaciones" ref="form" label-position="top" status-icon>
                    <!----------------------------------------------------------------------[INPUTS] -->
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Nombre" 
                                prop="nombre">
                                <el-input 
                                    v-model="status.formulario.data.nombre"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32" 
                                    maxlength="50"
                                    @input="handleInputStatusNombre"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                        <el-col :span="12">
                            <el-form-item 
                                label="Tipo" 
                                prop="tipo">
                                <el-input 
                                    v-model="status.formulario.data.tipo"
                                    onkeypress="return (event.charCode >= 65 && event.charCode <= 90) || (event.charCode >= 97 && event.charCode <= 122) || event.charCode == 32"
                                    maxlength="30"
                                    @input="handleInputStatusTipo"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>

                    </el-row>
                    <el-row :gutter="24">

                        <el-col :span="12">
                            <el-form-item 
                                label="Primer valor extra">
                                <el-input 
                                    v-model="status.formulario.data.valorExtra"
                                    maxlength="30"
                                    @input="handleInputStatusPrimerValor"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>   

                        <el-col :span="12">
                            <el-form-item 
                                label="Segundo valor extra">
                                <el-input 
                                    v-model="status.formulario.data.valorExtra2"
                                    maxlength="30"
                                    @input="handleInputStatusSegundoValor"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>  



                    </el-row>
                    <el-row :gutter="24" v-if="status.formulario.data.id">
                        <el-col :span="6">
                            <el-form-item 
                                label="Ordenamiento"
                                prop="ordenamiento">
                                <el-input 
                                    v-model="status.formulario.data.ordenamiento"
                                    maxlength="2"
                                    onkeypress="return event.charCode>=48 && event.charCode<=57"
                                    @input="handleInputStatusOrdenamiento"
                                    onpaste="return false">
                                </el-input>
                            </el-form-item>
                        </el-col>   
                    </el-row>

                    <div class="azt-form__separator"></div>
                    <el-row :gutter="24">
                        <el-col :span="24">
                            <el-form-item label="Descripcion:">
                                <el-input
                                type="textarea"
                                placeholder=""
                                v-model="status.formulario.data.descripcion"
                                @input="handleInputStatusDescripcion"
                                maxlength="100"
                                show-word-limit>
                                </el-input>
                            </el-form-item>
                        </el-col>   
                    </el-row>
                    <br>
                    <!----------------------------------------------------------------------[BOTONES] -->
                    <el-form-item class="azt-form__btn">
                        <el-button 
                            v-if="!status.formulario.data.id" 
                            type="success" 
                            :loading="status.formulario.visivilidad.loadingRegistrar" 
                            :disabled="!status.formulario.visivilidad.registrar"
                            @click="validarFormularioStatus('registrar')">
                            Registrar
                        </el-button>
                        <el-button 
                            v-if="status.formulario.data.id" 
                            type="success" 
                            :loading="status.formulario.visivilidad.loadingGuardar"
                            :disabled="!status.formulario.visivilidad.guardar"
                            @click="validarFormularioStatus('editar')">
                            Guardar
                        </el-button>
                    </el-form-item>
                </el-form>
            </el-dialog>
            <!--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------->
        </template>
        <template v-else>
            <div class="azt-process__error">
                <figure class="azt-process__img">
                    <picture>
                        <img src="~/static/img/page/undraw_building_blocks_re_5ahy.svg" alt="Azteca" />
                    </picture>
                </figure>
                <h3>La página está se encuentra fuera de servicios por el momento.</h3>
            </div>
        </template>
    </div>
</template>

<script>
import axios from 'axios';
let timeOutTableClasificaciones, timeOutTableConfiguraciones
let timeoutConfiguracionesLoadingPage
export default {
    name: 'Configuraciones',
    data() {
        return {
            configuraciones: {
                table: {
                    pagination: {
                        records: 0,
                        visibleRecordOptions: [
                            {
                                value: 20,
                                label: '20'
                            },
                            {
                                value: 50,
                                label: '50'
                            },
                            {
                                value: 80,
                                label: '80'
                            },
                            {
                                value: 100,
                                label: '100'
                            },
                    ],
                    pages: [
                        {
                            value: 0,
                            label: '1'
                        },
                    ],
                    },
                    data: null,
                    loading: true,
                    filtros: {
                        id: null,
                        nombre: null,
                        tipo: null,
                        sisStatus: null,
                        pagination: {
                            currentPage: 0,
                            visibleRecords: 20,
                        },
                        ordenarPor: "ultimos_registrados",
                        estadoOptions: [
                            {
                                value: null,
                                label: 'Ver Todos'
                            },
                            {
                                value: true,
                                label: 'Activos'
                            },
                            {
                                value: false,
                                label: 'Inactivos'
                            }
                        ],
                    },
                },
                formulario: {
                    data: {
                        id: null,
                        nombre: null,
                        tipo: null,
                        primerValor: null,
                        segundoValor: null,
                        descripcion: null,
                        sisStatus : null
                    },
                    dataTemporal: {
                        nombre: null,
                        tipo: null,
                        primerValor: null,
                        segundoValor: null,
                        descripcion: null,
                        sisStatus: null
                    },
                    validaciones: {
                        nombre: [
                            { required: true, message: 'Ingrese el nombre.', trigger: 'blur' },
                            { min: 3, message: 'El nombre debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 50, message: 'El nombre debe contener maximo 50 caracteres.', trigger: 'blur'}
                        ],
                        tipo: [
                            { required: true, message: 'Ingrese el tipo.', trigger: 'blur' },
                            { min: 3, message: 'El tipo debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 30, message: 'El tipo debe contener maximo 30 caracteres.', trigger: 'blur'}
                        ],
                        valor: [
                            { required: true, message: 'Ingrese el valor.', trigger: 'blur' },
                            { min: 3, message: 'El valor debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 50, message: 'El valor debe contener maximo 50 caracteres.', trigger: 'blur'}
                        ],
                    },
                    visivilidad: {
                        formulario: false,
                        guardar: false,
                        registrar: false,
                        loadingGuardar: false,
                        loadingRegistrar: false
                    },
                    loading: false
                }
            },
            clasificaciones: {
                table: {
                    pagination: {
                        records: 0,
                        visibleRecordOptions: [
                            {
                                value: 20,
                                label: '20'
                            },
                            {
                                value: 50,
                                label: '50'
                            },
                            {
                                value: 80,
                                label: '80'
                            },
                            {
                                value: 100,
                                label: '100'
                            },
                    ],
                    pages: [
                        {
                            value: 0,
                            label: '1'
                        },
                    ],
                    },
                    data: null,
                    loading: true,
                    filtros: {
                        id: null,
                        nombre: null,
                        pagination: {
                            currentPage: 0,
                            visibleRecords: 20,
                        },
                        ordenarPor: "ultimos_registrados",
                    },
                },
                formulario: {
                    data: {
                        id: null,
                        nombre: null,
                        descripcion: null,
                        tipo: null,
                        valor: null,
                        sisStatus : null
                    },
                    dataTemporal: {
                        nombre: null,
                        descripcion: null,
                        tipo: null,
                        valor: null,
                        sisStatus: null
                    },
                    validaciones: {
                        nombre: [
                            { required: true, message: 'Ingrese el nombre.', trigger: 'blur' },
                            { min: 3, message: 'El nombre debe contener 3 caracteres.', trigger: 'blur'},
                            { max: 100, message: 'El nombre debe contener maximo 100 caracteres.', trigger: 'blur'}
                            
                        ],
                        tipo: [
                            { required: true, message: 'Ingrese el tipo.', trigger: 'blur' },
                            { min: 3, message: 'El tipo debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 30, message: 'El tipo debe contener maximo 30 caracteres.', trigger: 'blur'}
                        ],
                        valor: [
                            { required: true, message: 'Ingrese el valor.', trigger: 'blur' },
                            { min: 4, message: 'El valor debe contener al menos 4 caracteres.', trigger: 'blur'},
                            { max: 7, message: 'El valor debe contener maximo 7 caracteres.', trigger: 'blur'}
                        ],
                    },
                    visivilidad: {
                        formulario: false,
                        guardar: false,
                        registrar: false,
                        loadingGuardar: false,
                        loadingRegistrar: false
                    },
                    loading: false
                }
            },
            monedas: {
                table: {
                    pagination: {
                        records: 0,
                        visibleRecordOptions: [
                            {
                                value: 20,
                                label: '20'
                            },
                            {
                                value: 50,
                                label: '50'
                            },
                            {
                                value: 80,
                                label: '80'
                            },
                            {
                                value: 100,
                                label: '100'
                            },
                    ],
                    pages: [
                        {
                            value: 0,
                            label: '1'
                        },
                    ],
                    },
                    data: null,
                    loading: true,
                    filtros: {
                        pagination: {
                            currentPage: 0,
                            visibleRecords: 20,
                        },
                        ordenarPor: "ultimos_registrados",
                    },
                },
                formulario: {
                    data: {
                        id: null,
                        clave: null,
                        descripcion: null,
                        sisStatus : null
                    },
                    dataTemporal: {
                        clave: null,
                        descripcion: null,
                        sisStatus: null
                    },
                    validaciones: {
                        clave: [
                            { required: true, message: 'Ingrese la clave.', trigger: 'blur' },
                            { min: 3, max: 3, message: 'El clave debe contener 3 caracteres.', trigger: 'blur'}
                        ]
                    },
                    visivilidad: {
                        formulario: false,
                        guardar: false,
                        registrar: false,
                        loadingGuardar: false,
                        loadingRegistrar: false
                    },
                    loading: false
                }
            },
            status: {
                table: {
                    pagination: {
                        records: 0,
                        visibleRecordOptions: [
                            {
                                value: 20,
                                label: '20'
                            },
                            {
                                value: 50,
                                label: '50'
                            },
                            {
                                value: 80,
                                label: '80'
                            },
                            {
                                value: 100,
                                label: '100'
                            },
                    ],
                    pages: [
                        {
                            value: 0,
                            label: '1'
                        },
                    ],
                    },
                    data: null,
                    loading: true,
                    filtros: {
                        pagination: {
                            currentPage: 0,
                            visibleRecords: 20,
                        },
                        ordenarPor: "ultimos_registrados",
                    },
                },
                formulario: {
                    data: {
                        id: null,
                        nombre: null,
                        tipo : null,
                        valorExtra : null,
                        valorExtra2 : null,
                        ordenamiento : null,
                        descripcion : null,
                        sisStatus : null
                    },
                    dataTemporal: {
                        nombre: null,
                        tipo : null,
                        descripcion : null,
                        valorExtra : null,
                        valorExtra2 : null,
                        ordenamiento : null,
                        sisStatus : null
                    },
                    validaciones: {
                        nombre: [
                            { required: true, message: 'Ingrese la nombre.', trigger: 'blur' },
                            { min: 3, message: 'El nombre debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 120, message: 'El nombre debe contener maximo 50 caracteres.', trigger: 'blur'}
                        ],
                        tipo: [
                            { required: true, message: 'Ingrese la tipo.', trigger: 'blur' },
                            { min: 3, message: 'El tipo debe contener al menos 3 caracteres.', trigger: 'blur'},
                            { max: 50, message: 'El tipo debe contener maximo 50 caracteres.', trigger: 'blur'}
                        ],
                        ordenamiento: [
                            { required: true, message: 'Ingrese el ordenamiento.', trigger: 'blur' }
                        ]
                    },
                    visivilidad: {
                        formulario: false,
                        guardar: false,
                        registrar: false,
                        loadingGuardar: false,
                        loadingRegistrar: false
                    },
                    loading: false
                }
            },
            page:{
                loaded: false,
                interval: null,
            },
        }
    },
    computed: {
        user() {
            return this.$store.state.user;
        },
        currentModule() {
            return this.$store.state.currentModule;
        },
        currentProcess() {
            return this.$store.state.currentProcess;
        },
        accessProcesses() {
            return this.$store.state.accessProcesses;
        },
        dateFormat() {
            return this.$store.state.dateFormat;
        }
    },
    watch: {
        'configuraciones.table.filtros': {
            handler: function (val, oldVal) {
                if (this.page.loaded) this.actualizarTablaFiltrosConfiguraciones();
            },
            deep: true        
        },
        'clasificaciones.table.filtros': {
            handler: function (val, oldVal) {
                if (this.page.loaded) this.actualizarTablaFiltrosClasificaciones();
            },
            deep: true        
        },
        'configuraciones.formulario.data': {
            handler: function (val, oldVal) {
                if(this.configuraciones.formulario.data.nombre != this.configuraciones.formulario.dataTemporal.nombre || 
                   this.configuraciones.formulario.data.tipo != this.configuraciones.formulario.dataTemporal.tipo || 
                   this.configuraciones.formulario.data.primerValor != this.configuraciones.formulario.dataTemporal.primerValor ||
                   this.configuraciones.formulario.data.segundoValor != this.configuraciones.formulario.dataTemporal.segundoValor ||  
                   (this.configuraciones.formulario.data.descripcion != this.configuraciones.formulario.dataTemporal.descripcion || (this.clasificaciones.formulario.dataTemporal.descripcion === null && this.clasificaciones.formulario.data.descripcion === ''))
                )
                {
                    this.configuraciones.formulario.visivilidad.registrar = true
                    this.configuraciones.formulario.visivilidad.guardar = true
                }else{
                    this.configuraciones.formulario.visivilidad.registrar = false
                    this.configuraciones.formulario.visivilidad.guardar = false

                }
            },
            deep: true        
        },
        'clasificaciones.formulario.data': {
            handler: function (val, oldVal) {
                if(this.clasificaciones.formulario.data.nombre != this.clasificaciones.formulario.dataTemporal.nombre || 
                   this.clasificaciones.formulario.data.tipo != this.clasificaciones.formulario.dataTemporal.tipo || 
                   this.clasificaciones.formulario.data.valor != this.clasificaciones.formulario.dataTemporal.valor || 
                   (this.clasificaciones.formulario.data.descripcion != this.clasificaciones.formulario.dataTemporal.descripcion || (this.clasificaciones.formulario.dataTemporal.descripcion === null && this.clasificaciones.formulario.data.descripcion === ''))
                )
                {
                    this.clasificaciones.formulario.visivilidad.registrar = true
                    this.clasificaciones.formulario.visivilidad.guardar = true
                }else{
                    this.clasificaciones.formulario.visivilidad.registrar = false
                    this.clasificaciones.formulario.visivilidad.guardar = false

                }
            },
            deep: true        
        },
        'monedas.formulario.data': {
            handler: function (val, oldVal) {
                if(this.monedas.formulario.data.clave != this.monedas.formulario.dataTemporal.clave || 
                   (this.monedas.formulario.data.descripcion != this.monedas.formulario.dataTemporal.descripcion || (this.monedas.formulario.dataTemporal.descripcion === null && this.monedas.formulario.data.descripcion === ''))
                )
                {
                    this.monedas.formulario.visivilidad.registrar = true
                    this.monedas.formulario.visivilidad.guardar = true
                }else{
                    this.monedas.formulario.visivilidad.registrar = false
                    this.monedas.formulario.visivilidad.guardar = false

                }
            },
            deep: true        
        },
        'status.formulario.data': {
            handler: function (val, oldVal) {
                if(this.status.formulario.data.nombre != this.status.formulario.dataTemporal.nombre || 
                    this.status.formulario.data.tipo != this.status.formulario.dataTemporal.tipo ||
                    this.status.formulario.data.valorExtra != this.status.formulario.dataTemporal.valorExtra ||
                    this.status.formulario.data.valorExtra2 != this.status.formulario.dataTemporal.valorExtra2 ||
                    this.status.formulario.data.ordenamiento != this.status.formulario.dataTemporal.ordenamiento || 
                   (this.status.formulario.data.descripcion != this.status.formulario.dataTemporal.descripcion || (this.status.formulario.dataTemporal.descripcion === null && this.status.formulario.data.descripcion === ''))
                )
                {
                    this.status.formulario.visivilidad.registrar = true
                    this.status.formulario.visivilidad.guardar = true
                }else{
                    this.status.formulario.visivilidad.registrar = false
                    this.status.formulario.visivilidad.guardar = false

                }
            },
            deep: true        
        }
    },
    methods: {
        // --------------------------------------------------------------------[CONFIGURACIONES]
        async consultarConfiguraciones() {
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;
            
            var url = urlBase + ":" + apiModule + '/ventasCatalogos/catalogos/configuraciones/verConfiguraciones/' + this.user.id

            try {
                let res = await axios.get(url);
                var result = res.data;
                if (result.status) {
                    this.configuraciones.table.data = result.data;
                    this.configuraciones.table.loading = false;
                    this.configuraciones.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.configuraciones.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.configuraciones.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarConfiguracionesFiltros(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;

            let id = this.configuraciones.table.filtros.id == null || this.configuraciones.table.filtros.id == '' ? 0 : this.configuraciones.table.filtros.id
            let nombre = this.configuraciones.table.filtros.nombre == '' ? null : this.configuraciones.table.filtros.nombre
            let tipo = this.configuraciones.table.filtros.tipo == '' ? null : this.configuraciones.table.filtros.tipo
            let sisStatus = this.configuraciones.table.filtros.sisStatus

            var body = {
                 id: parseInt(id),
                 nombre: nombre,
                 tipo: tipo,
                 sisStatus: sisStatus
            }

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/configuraciones/verConfiguracionesFiltros/${this.user.id}/${this.configuraciones.table.filtros.ordenarPor}/${this.configuraciones.table.filtros.pagination.currentPage}/${this.configuraciones.table.filtros.pagination.visibleRecords}`;
            
            try {
                let res = await axios.post(url,body);
                var result = res.data;
                if (result.status) {
                    this.configuraciones.table.data = result.data;
                    this.configuraciones.table.loading = false;
                    this.configuraciones.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.configuraciones.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.configuraciones.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarConfiguracion(id){
            this.monedas.formulario.data.id = id;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/configuraciones/verConfiguracion/${id}/${this.user.id}`;

            try {
                this.configuraciones.formulario.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                this.configuraciones.formulario.loading = false;
                
                if (result.status) {
                    let data = result.data[0];
                    this.configuraciones.formulario.data.id = data.id
                    this.configuraciones.formulario.data.nombre = data.nombre
                    this.configuraciones.formulario.data.tipo = data.tipo
                    this.configuraciones.formulario.data.primerValor = data.valor1
                    this.configuraciones.formulario.data.segundoValor = data.valor2
                    this.configuraciones.formulario.data.descripcion = data.descripcion
                    this.configuraciones.formulario.data.sisStatus = data.sisStatus
                    // [DATA TEMPORAL]
                    this.configuraciones.formulario.dataTemporal.nombre = data.nombre;
                    this.configuraciones.formulario.dataTemporal.tipo = data.tipo;
                    this.configuraciones.formulario.dataTemporal.primerValor = data.valor1;
                    this.configuraciones.formulario.dataTemporal.segundoValor = data.valor2;
                    this.configuraciones.formulario.dataTemporal.descripcion = data.descripcion;
                    this.configuraciones.formulario.dataTemporal.sisStatus = data.sisStatus;
                } else {
                    var msg = result.message;
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async registrarConfiguracion(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.configuraciones.formulario.visivilidad.loadingRegistrar = true


            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/configuraciones/registrar/${this.user.id}`;

            let body = {
                nombre: this.configuraciones.formulario.data.nombre,
                tipo: this.configuraciones.formulario.data.tipo,
                valor1: (this.configuraciones.formulario.data.primerValor == '') ? null : this.configuraciones.formulario.data.primerValor,
                valor2: (this.configuraciones.formulario.data.segundoValor == '') ? null : this.configuraciones.formulario.data.segundoValor,
                descripcion: (this.configuraciones.formulario.data.descripcion == '' ? null : this.configuraciones.formulario.data.descripcion),
            }
            
            try {
                let res = await axios.post(url,body);
                var result = res.data;
                this.clasificaciones.formulario.loading = false;
                if (result.status) {
                    this.limpiarDataConfiguraciones()
                    this.updateViwSettings();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.configuraciones.formulario.visivilidad.loadingRegistrar = false
                    this.configuraciones.formulario.visivilidad.formulario = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.configuraciones.formulario.visivilidad.loadingRegistrar = false
                }
            } catch (error) {
                this.configuraciones.formulario.visivilidad.loadingRegistrar = false
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
            }
        },
        async editarConfiguracion(){
            let action = action;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.configuraciones.formulario.visivilidad.loadingGuardar = true

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/configuraciones/editar/${this.user.id}`;
            
            let nombre = this.configuraciones.formulario.data.nombre
            nombre = (this.configuraciones.formulario.data.nombre == this.configuraciones.formulario.dataTemporal.nombre ) ? null : nombre

            let tipo = this.configuraciones.formulario.data.tipo
            tipo = (this.configuraciones.formulario.data.tipo == this.configuraciones.formulario.dataTemporal.tipo ) ? null : tipo

            if(this.configuraciones.formulario.visivilidad.formulario){
                if(this.configuraciones.formulario.data.nombre != this.configuraciones.formulario.dataTemporal.nombre){
                    nombre = this.configuraciones.formulario.data.nombre
                    tipo = this.configuraciones.formulario.data.tipo
                }else if(this.configuraciones.formulario.data.tipo != this.configuraciones.formulario.dataTemporal.tipo){
                    nombre = this.configuraciones.formulario.data.nombre
                    tipo = this.configuraciones.formulario.data.tipo
                }
            }

            let primerValor = this.configuraciones.formulario.data.primerValor
            primerValor = (this.configuraciones.formulario.data.primerValor == this.configuraciones.formulario.dataTemporal.primerValor ) ? null : primerValor

            let segundoValor = this.configuraciones.formulario.data.segundoValor
            segundoValor = (this.configuraciones.formulario.data.segundoValor == this.configuraciones.formulario.dataTemporal.segundoValor ) ? null : segundoValor

            let descripcion = this.configuraciones.formulario.data.descripcion
            descripcion = (this.configuraciones.formulario.data.descripcion == this.configuraciones.formulario.dataTemporal.descripcion ) ? null : descripcion

            let sisStatus = this.configuraciones.formulario.data.sisStatus
            sisStatus = (this.configuraciones.formulario.data.sisStatus == this.configuraciones.formulario.dataTemporal.sisStatus ) ? null : sisStatus

            let body = {
                id: this.configuraciones.formulario.data.id,
                nombre: nombre,
                tipo: tipo,
                valor1: primerValor,
                valor2: segundoValor,
                descripcion: descripcion,
                sisStatus: sisStatus,
            }

            try {
                let res = await axios.put(url,body);
                var result = res.data;
                if (result.status) {
                    if(this.configuraciones.formulario.visivilidad.formulario){
                        this.consultarConfiguracion(result.data[0].id)
                    }
                    this.consultarConfiguracionesFiltros();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.configuraciones.formulario.visivilidad.guardar = false
                    this.configuraciones.formulario.visivilidad.loadingGuardar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.configuraciones.formulario.visivilidad.loadingGuardar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.configuraciones.formulario.visivilidad.loadingGuardar = false
            }
        },
        async eliminarConfiguracion(id){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/configuraciones/eliminar/${this.user.id}/${id}`;

            try {
                let res = await axios.delete(url);
                var result = res.data;
                
                if (result.status) {
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });

                    this.consultarConfiguracionesFiltros();

                } else {
                    var msg = result.message
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message :  msg});

                    instance.confirmButtonLoading = false;
                    instance.confirmButtonText = 'Aceptar';
                    done();
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        // --------------------------------------------------------------------[FUNSIONES]
        opcionesTableConfiguraciones(command){
            var action = command.action
            var data = command.data
            switch (action) {
                case 'editar':
                    this.configuraciones.formulario.visivilidad.formulario = true;
                    this.consultarConfiguracion(data.id);
                    break;
                case 'eliminar':
                    const h = this.$createElement;
                    this.$msgbox({
                        title: 'Eliminar',
                        message: h('p', null, [
                            h('span', null, '¿Deseas eliminar esta configuracion?')
                        ]),
                        type: 'warning',
                        showCancelButton: true,
                        confirmButtonText: 'Aceptar',
                        cancelButtonText: 'Cancelar',
                        beforeClose: (action, instance, done) => {
                            if (action === 'confirm') {
                                instance.confirmButtonLoading = true;
                                instance.confirmButtonText = 'Desactivando...';
                                this.eliminarConfiguracion(data.id);
                                setTimeout(() => {
                                    done();
                                    setTimeout(() => {
                                        instance.confirmButtonLoading = false;
                                    }, 300);
                                }, 1000);
                            } else {
                                done();
                            }
                        }
                    })
                    break;
                default:
                    this.$store.dispatch('getStatusAlert', { message :  'Esta opción no está disponible en este momento.'});
                    break;
            }
        },
        actualizarTablaFiltrosConfiguraciones() {
            this.configuraciones.table.loading = true;
            clearInterval(this.page.interval);
            clearTimeout(timeOutTableConfiguraciones);
            timeOutTableConfiguraciones = setTimeout(() => {
                this.consultarConfiguracionesFiltros();
                clearTimeout(timeOutTableConfiguraciones);
            }, 500);
        },
        cambiarEstadoConfiguracion(data){
            this.configuraciones.formulario.data.id = data.id
            let sisStatus = data.sisStatus
           
            if(sisStatus){
                this.configuraciones.formulario.data.sisStatus = true;
                this.editarConfiguracion();
                this.limpiarDataConfiguraciones();

            }else{
                const h = this.$createElement;
                this.$msgbox({
                    title: 'Estado',
                    message: h('p', null, [
                        h('span', null, '¿Deseas desativar esta configuracion?')
                    ]),
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Aceptar',
                    cancelButtonText: 'Cancelar',
                    beforeClose: (action, instance, done) => {
                        if (action === 'confirm') {
                            instance.confirmButtonLoading = true;
                            instance.confirmButtonText = 'Desactivando...';

                            this.configuraciones.formulario.data.sisStatus = false;
                            this.editarConfiguracion();
                            this.limpiarDataConfiguraciones();
                            setTimeout(() => {
                                done();
                                setTimeout(() => {
                                    instance.confirmButtonLoading = false;
                                }, 300);
                            }, 1000);
                        } else {
                            done();
                        }
                    }
                })
            }
        },
        validarFormularioConfiguraciones(action) {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    if (action == 'registrar') {
                        this.registrarConfiguracion()
                    }
                    else if (action == 'editar'){
                        this.editarConfiguracion()
                    }
                } else {
                    this.configuraciones.formulario.loading = false;
                    return false;
                }
            });
        },
        limpiarDataConfiguraciones(){
            this.configuraciones.formulario.data.id = null;
            this.configuraciones.formulario.data.nombre = null;
            this.configuraciones.formulario.data.descripcion = null;
            this.configuraciones.formulario.data.tipo = null;
            this.configuraciones.formulario.data.primerValor = null;
            this.configuraciones.formulario.data.segundoValor = null;
            this.configuraciones.formulario.data.sisStatus = null;
            this.configuraciones.formulario.dataTemporal.nombre = null;
            this.configuraciones.formulario.dataTemporal.descripcion = null;
            this.configuraciones.formulario.dataTemporal.tipo = null;
            this.configuraciones.formulario.dataTemporal.primerValor = null;
            this.configuraciones.formulario.dataTemporal.segundoValor = null;
            this.configuraciones.formulario.dataTemporal.sisStatus = null;
        },
        handleInputConfiguracionNombre(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.configuraciones.formulario.data.nombre = value;

            if( this.configuraciones.formulario.dataTemporal.nombre == null && this.configuraciones.formulario.data.nombre == ''){
                this.configuraciones.formulario.data.nombre = null
            }
        },
        handleInputConfiguracionTipo(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.configuraciones.formulario.data.tipo = value;

            if( this.configuraciones.formulario.dataTemporal.tipo == null && this.configuraciones.formulario.data.tipo == ''){
                this.configuraciones.formulario.data.tipo = null
            }
        },
        handleInputConfiguracionPrimerValor(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0) + value.slice(1);
            }
            this.configuraciones.formulario.data.primerValor = value;

            if( this.configuraciones.formulario.dataTemporal.primerValor == null && this.configuraciones.formulario.data.primerValor == ''){
                this.configuraciones.formulario.data.primerValor = null
            }
        },
        handleInputConfiguracionSegundoValor(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0) + value.slice(1);
            }
            this.configuraciones.formulario.data.segundoValor = value;

            if( this.configuraciones.formulario.dataTemporal.segundoValor == null && this.configuraciones.formulario.data.segundoValor == ''){
                this.configuraciones.formulario.data.segundoValor = null
            }
        },
        handleInputConfiguracionDescripcion(value){
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.configuraciones.formulario.data.descripcion = value;

            if( this.configuraciones.formulario.dataTemporal.descripcion == null && this.configuraciones.formulario.data.descripcion == ''){
                this.configuraciones.formulario.data.descripcion = null
            }
        },
        // --------------------------------------------------------------------[CLASIFICACIONES]
        async consultarClasificaciones() {
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;
            var url = urlBase + ":" + apiModule + '/ventasCatalogos/catalogos/clasificaciones/verClasificaciones/' + this.user.id


            try {
                let res = await axios.get(url);

                var result = res.data;
                if (result.status) {
                    this.clasificaciones.table.data = result.data;
                    this.clasificaciones.table.loading = false;
                    this.clasificaciones.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.clasificaciones.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.clasificaciones.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarClasificacionesFiltros(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            let id = this.clasificaciones.table.filtros.id == null || this.clasificaciones.table.filtros.id == "" ? 0 : this.clasificaciones.table.filtros.id
            let nombre = this.clasificaciones.table.filtros.nombre == null ? '' : this.clasificaciones.table.filtros.nombre
            var body = {
                 id: parseInt(id),
                 nombre: nombre
            }
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/clasificaciones/verClasificacionesFiltros/${this.user.id}/${this.clasificaciones.table.filtros.ordenarPor}/${this.clasificaciones.table.filtros.pagination.currentPage}/${this.clasificaciones.table.filtros.pagination.visibleRecords}`;
            
            try {
                let res = await axios.post(url,body);
                var result = res.data;
                if (result.status) {
                    this.clasificaciones.table.data = result.data;
                    this.clasificaciones.table.loading = false;
                    this.clasificaciones.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.clasificaciones.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.clasificaciones.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarClasificacion(id){
            this.clasificaciones.formulario.data.id = id;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/clasificaciones/verClasificacion/${id}/${this.user.id}`;
            try {
                
                this.clasificaciones.formulario.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                this.clasificaciones.formulario.loading = false;
                
                if (result.status) {
                    let data = result.data[0];
                    this.clasificaciones.formulario.data.id = data.id
                    this.clasificaciones.formulario.data.nombre = data.nombre
                    this.clasificaciones.formulario.data.descripcion = data.descripcion
                    this.clasificaciones.formulario.data.tipo = data.tipo
                    this.clasificaciones.formulario.data.valor = data.valor
                    this.clasificaciones.formulario.data.sisStatus = data.sisStatus
                    // [DATA TEMPORAL]
                    this.clasificaciones.formulario.dataTemporal.nombre = data.nombre;
                    this.clasificaciones.formulario.dataTemporal.tipo = data.tipo;
                    this.clasificaciones.formulario.dataTemporal.valor = data.valor;
                    this.clasificaciones.formulario.dataTemporal.descripcion = data.descripcion;
                    this.clasificaciones.formulario.dataTemporal.sisStatus = data.sisStatus;
                } else {
                    var msg = result.message;
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async registrarClasificacion(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.clasificaciones.formulario.visivilidad.loadingRegistrar = true
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/clasificaciones/registrar/${this.user.id}`;

            let body = {
                nombre: this.clasificaciones.formulario.data.nombre,
                tipo: this.clasificaciones.formulario.data.tipo,
                valor: this.clasificaciones.formulario.data.valor,
                descripcion: (this.clasificaciones.formulario.data.descripcion == null ? '' : this.clasificaciones.formulario.data.descripcion),
            }

            try {
                let res = await axios.post(url,body);
                var result = res.data;
                this.clasificaciones.formulario.loading = false;
                if (result.status) {
                    this.limpiarDataClasificaciones()
                    this.updateViwSettings();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.clasificaciones.formulario.visivilidad.formulario = false
                    this.clasificaciones.formulario.visivilidad.loadingRegistrar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.clasificaciones.formulario.visivilidad.loadingRegistrar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.clasificaciones.formulario.visivilidad.loadingRegistrar = false
            }
        },
        async editarClasificacion(){
            let action = action;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.clasificaciones.formulario.visivilidad.loadingGuardar = true
            
            let nombre = this.clasificaciones.formulario.data.nombre
            nombre = (this.clasificaciones.formulario.data.nombre == this.clasificaciones.formulario.dataTemporal.nombre ) ? null : nombre

            let tipo = this.clasificaciones.formulario.data.tipo
            tipo = (this.clasificaciones.formulario.data.tipo == this.clasificaciones.formulario.dataTemporal.tipo ) ? null : tipo

            if(this.clasificaciones.formulario.visivilidad.formulario){
                if(this.clasificaciones.formulario.data.nombre != this.clasificaciones.formulario.dataTemporal.nombre){
                    nombre = this.clasificaciones.formulario.data.nombre
                    tipo = this.clasificaciones.formulario.data.tipo
                }else if(this.clasificaciones.formulario.data.tipo != this.clasificaciones.formulario.dataTemporal.tipo){
                    nombre = this.clasificaciones.formulario.data.nombre
                    tipo = this.clasificaciones.formulario.data.tipo
                }
            }

            let valor = this.clasificaciones.formulario.data.valor
            valor = (this.clasificaciones.formulario.data.valor == this.clasificaciones.formulario.dataTemporal.valor ) ? null : valor

            let descripcion = this.clasificaciones.formulario.data.descripcion
            descripcion = (this.clasificaciones.formulario.data.descripcion == this.clasificaciones.formulario.dataTemporal.descripcion ) ? null : descripcion

            let sisStatus = this.clasificaciones.formulario.data.sisStatus
            sisStatus = (this.clasificaciones.formulario.data.sisStatus == this.clasificaciones.formulario.dataTemporal.sisStatus ) ? null : sisStatus

            if(nombre != null){nombre = nombre.trim()}
            if(tipo != null){tipo = tipo.trim()}
            if(descripcion && descripcion.length != 0 ){descripcion = descripcion.trim()}

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/clasificaciones/editar/${this.user.id}`;

            let body = {
                id: this.clasificaciones.formulario.data.id,
                nombre: nombre,
                tipo: tipo,
                valor: valor,
                descripcion: descripcion,
                sisStatus: sisStatus,
            }

            try {
                let res = await axios.put(url,body);
                var result = res.data;
                if (result.status) {
                    if(this.clasificaciones.formulario.visivilidad.formulario){
                        this.consultarClasificacion(result.data[0].id)
                    }
                    this.consultarClasificacionesFiltros();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.clasificaciones.formulario.visivilidad.guardar = false
                    this.clasificaciones.formulario.visivilidad.loadingGuardar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.clasificaciones.formulario.visivilidad.loadingGuardar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.clasificaciones.formulario.visivilidad.loadingGuardar = false
            }
        },
        async eliminarClasificacion(id){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/clasificaciones/eliminar/${this.user.id}/${id}`;

            try {
                let res = await axios.delete(url);
                var result = res.data;
                
                if (result.status) {
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });

                    this.actualizarTablaFiltrosClasificaciones();
                } else {
                    var msg = result.message
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message :  msg});

                    instance.confirmButtonLoading = false;
                    instance.confirmButtonText = 'Aceptar';
                    done();
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        // --------------------------------------------------------------------[FUNSIONES]
        opcionesTableClasificaciones(command){
            var action = command.action
            var data = command.data
            switch (action) {
                case 'editar':
                    this.clasificaciones.formulario.visivilidad.formulario = true;
                    this.consultarClasificacion(data.id);
                    break;
                case 'eliminar':
                    const h = this.$createElement;
                    this.$msgbox({
                        title: 'Eliminar',
                        message: h('p', null, [
                            h('span', null, '¿Deseas eliminar esta clasificacion?')
                        ]),
                        type: 'warning',
                        showCancelButton: true,
                        confirmButtonText: 'Aceptar',
                        cancelButtonText: 'Cancelar',
                        beforeClose: (action, instance, done) => {
                            if (action === 'confirm') {
                                instance.confirmButtonLoading = true;
                                instance.confirmButtonText = 'Desactivando...';
                                this.eliminarClasificacion(data.id);
                                setTimeout(() => {
                                    done();
                                    setTimeout(() => {
                                        instance.confirmButtonLoading = false;
                                    }, 300);
                                }, 1000);
                            } else {
                                done();
                            }
                        }
                    })
                    break;
                default:
                    this.$store.dispatch('getStatusAlert', { message :  'Esta opción no está disponible en este momento.'});
                    break;
            }
        },
        validarFormularioclasificaciones(action) {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    if (action == 'registrar') {
                        this.registrarClasificacion()
                    }
                    else if (action == 'editar'){
                        this.editarClasificacion()
                    }
                } else {
                    this.clasificaciones.formulario.loading = false;
                    return false;
                }
            });
        },
        limpiarDataClasificaciones(){
            this.clasificaciones.formulario.data.id = null;
            this.clasificaciones.formulario.data.nombre = null;
            this.clasificaciones.formulario.data.descripcion = null;
            this.clasificaciones.formulario.data.tipo = null;
            this.clasificaciones.formulario.data.valor = null;
            this.clasificaciones.formulario.data.sisStatus = null;
            this.clasificaciones.formulario.dataTemporal.nombre = null;
            this.clasificaciones.formulario.dataTemporal.descripcion = null;
            this.clasificaciones.formulario.dataTemporal.tipo = null;
            this.clasificaciones.formulario.dataTemporal.valor = null;
            this.clasificaciones.formulario.dataTemporal.sisStatus = null;
        },
        actualizarTablaFiltrosClasificaciones() {
            this.clasificaciones.table.loading = true;
            clearInterval(this.page.interval);
            clearTimeout(timeOutTableClasificaciones);
            timeOutTableClasificaciones = setTimeout(() => {
                this.consultarClasificacionesFiltros();
                clearTimeout(timeOutTableClasificaciones);
            }, 500);
        },
        cambiarEstadoClasificacion(data){
            this.clasificaciones.formulario.data.id = data.id
            let sisStatus = data.sisStatus

           
            if(sisStatus){
                this.clasificaciones.formulario.data.sisStatus = true;
                this.editarClasificacion();
                this.limpiarDataClasificaciones();
            }else{
                const h = this.$createElement;
                this.$msgbox({
                    title: 'Estado',
                    message: h('p', null, [
                        h('span', null, '¿Deseas desativar esta clasificacion?')
                    ]),
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Aceptar',
                    cancelButtonText: 'Cancelar',
                    beforeClose: (action, instance, done) => {
                        if (action === 'confirm') {
                            instance.confirmButtonLoading = true;
                            instance.confirmButtonText = 'Desactivando...';

                            this.clasificaciones.formulario.data.sisStatus = false;
                            this.editarClasificacion();
                            this.limpiarDataClasificaciones();
                            setTimeout(() => {
                                done();
                                setTimeout(() => {
                                    instance.confirmButtonLoading = false;
                                }, 300);
                            }, 1000);
                        } else {
                            done();
                        }
                    }
                })
            }
        },
        handleInputClasificacionesNombre(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.clasificaciones.formulario.data.nombre = value;

            if( this.clasificaciones.formulario.dataTemporal.nombre == null && this.clasificaciones.formulario.data.nombre == ''){
                this.clasificaciones.formulario.data.nombre = null
            }
        },
        handleInputClasificacionesTipo(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.clasificaciones.formulario.data.tipo = value;

            if( this.clasificaciones.formulario.dataTemporal.tipo == null && this.clasificaciones.formulario.data.tipo == ''){
                this.clasificaciones.formulario.data.tipo = null
            }
        },
        handleInputClasificacionesValor(value) {
            value = value.trimStart();
            if (value === '#') {
                this.clasificaciones.formulario.data.valor = '';

                if( this.clasificaciones.formulario.dataTemporal.valor == null && this.clasificaciones.formulario.data.valor == ''){
                    this.clasificaciones.formulario.data.valor = null
                }
                return;
            }
            if (!value.startsWith('#')) {
                value = '#' + value;
            }
            this.clasificaciones.formulario.data.valor = value;
        },
        handleInputClasificacionesDescripcion(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.clasificaciones.formulario.data.descripcion = value;

            if( this.clasificaciones.formulario.dataTemporal.descripcion == null && this.clasificaciones.formulario.data.descripcion == ''){
                this.clasificaciones.formulario.data.descripcion = null
            }
        },
        // --------------------------------------------------------------------[MONEDAS]
        async consultarMonedas(){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;
            var url = urlBase + ":" + apiModule + '/ventasCatalogos/catalogos/moneda/verMonedas/' + this.user.id


            try {
                this.monedas.table.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                if (result.status) {
                    this.monedas.table.data = result.data;
                    this.monedas.table.loading = false;
                    this.monedas.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.monedas.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.monedas.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarMoneda(id){
            this.monedas.formulario.data.id = id;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/moneda/verMoneda/${id}/${this.user.id}`;
            try {
                this.monedas.formulario.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                this.monedas.formulario.loading = false;
                if (result.status) {
                    let data = result.data[0];
                    this.monedas.formulario.data.id = data.id
                    this.monedas.formulario.data.clave = data.clave
                    this.monedas.formulario.data.descripcion = data.descripcion
                    // [DATA TEMPORAL]
                    this.monedas.formulario.dataTemporal.clave = data.clave;
                    this.monedas.formulario.dataTemporal.descripcion = data.descripcion;
                    this.monedas.formulario.dataTemporal.sisStatus = data.sisStatus;
                } else {
                    var msg = result.message;
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async registrarMoneda(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.monedas.formulario.visivilidad.loadingRegistrar = true
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/moneda/registrar/${this.user.id}`;

            let body = {
                clave: this.monedas.formulario.data.clave,
                descripcion: (this.monedas.formulario.data.descripcion == null ? '' : this.monedas.formulario.data.descripcion),
            }

            try {
                let res = await axios.post(url,body);
                var result = res.data;
                this.monedas.formulario.loading = false;
                if (result.status) {
                    this.monedas.formulario.visivilidad.formulario = false
                    this.limpiarDataMonedas()
                    this.updateViwSettings();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.monedas.formulario.visivilidad.formulario = false
                    this.monedas.formulario.visivilidad.loadingRegistrar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.monedas.formulario.visivilidad.loadingRegistrar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.monedas.formulario.visivilidad.loadingRegistrar = false
            }
        },
        async editarMoneda(){
            let action = action;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.monedas.formulario.visivilidad.loadingGuardar = true

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/moneda/editar/${this.user.id}`;
            
            let clave = this.monedas.formulario.data.clave
            clave = (this.monedas.formulario.data.clave == this.monedas.formulario.dataTemporal.clave ) ? null : clave

            let descripcion = this.monedas.formulario.data.descripcion
            descripcion = (this.monedas.formulario.data.descripcion == this.monedas.formulario.dataTemporal.descripcion ) ? null : descripcion

            let sisStatus = this.monedas.formulario.data.sisStatus
            sisStatus = (this.monedas.formulario.data.sisStatus == this.monedas.formulario.dataTemporal.sisStatus ) ? null : sisStatus

            let body = {
                id: this.monedas.formulario.data.id,
                clave: clave,
                descripcion: descripcion,
                sisStatus: sisStatus,
            }

            try {
                let res = await axios.put(url,body);
                var result = res.data;
                if (result.status) {
                    if(this.monedas.formulario.visivilidad.formulario){
                        this.consultarMoneda(result.data[0].id)
                    }
                    this.consultarMonedas();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.monedas.formulario.visivilidad.guardar = false
                    this.monedas.formulario.visivilidad.loadingGuardar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.monedas.formulario.visivilidad.loadingGuardar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.monedas.formulario.visivilidad.loadingGuardar = false
            }
        },
        async eliminarMoneda(id){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/moneda/eliminar/${this.user.id}/${id}`;

            try {
                let res = await axios.delete(url);
                var result = res.data;
                
                if (result.status) {
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });

                    this.consultarMonedas();
                } else {
                    var msg = result.message
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message :  msg});

                    instance.confirmButtonLoading = false;
                    instance.confirmButtonText = 'Aceptar';
                    done();
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        // --------------------------------------------------------------------[FUNSIONES]
        opcionesTableMonedas(command){
            var action = command.action
            var data = command.data
            switch (action) {
                case 'editar':
                    this.monedas.formulario.visivilidad.formulario = true;
                    this.consultarMoneda(data.id);
                    break;
                case 'eliminar':
                    const h = this.$createElement;
                    this.$msgbox({
                        title: 'Eliminar',
                        message: h('p', null, [
                            h('span', null, '¿Deseas eliminar esta moneda?')
                        ]),
                        type: 'warning',
                        showCancelButton: true,
                        confirmButtonText: 'Aceptar',
                        cancelButtonText: 'Cancelar',
                        beforeClose: (action, instance, done) => {
                            if (action === 'confirm') {
                                instance.confirmButtonLoading = true;
                                instance.confirmButtonText = 'Desactivando...';
                                this.eliminarMoneda(data.id);
                                setTimeout(() => {
                                    done();
                                    setTimeout(() => {
                                        instance.confirmButtonLoading = false;
                                    }, 300);
                                }, 1000);
                            } else {
                                done();
                            }
                        }
                    })
                    break;
                default:
                    this.$store.dispatch('getStatusAlert', { message :  'Esta opción no está disponible en este momento.'});
                    break;
            }
        },
        validarFormularioMonedas(action) {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    if (action == 'registrar') {
                        this.registrarMoneda()
                    }
                    else if (action == 'editar'){
                        this.editarMoneda()
                    }
                } else {
                    this.monedas.formulario.loading = false;
                    return false;
                }
            });
        },
        limpiarDataMonedas(){
            this.monedas.formulario.data.id = null;
            this.monedas.formulario.data.clave = null;
            this.monedas.formulario.data.descripcion = null;
            this.monedas.formulario.dataTemporal.sisStatus = null;
            this.monedas.formulario.dataTemporal.clave = null;
            this.monedas.formulario.dataTemporal.descripcion = null;
            this.monedas.formulario.dataTemporal.sisStatus = null;
        },
        cambiarEstadoMoneda(data){
            this.monedas.formulario.data.id = data.id
            let sisStatus = data.sisStatus

           
            if(sisStatus){
                this.monedas.formulario.data.sisStatus = true;
                this.editarMoneda();
                this.limpiarDataMonedas();

            }else{
                const h = this.$createElement;
                this.$msgbox({
                    title: 'Estado',
                    message: h('p', null, [
                        h('span', null, '¿Deseas desativar esta moenda?')
                    ]),
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Aceptar',
                    cancelButtonText: 'Cancelar',
                    beforeClose: (action, instance, done) => {
                        if (action === 'confirm') {
                            instance.confirmButtonLoading = true;
                            instance.confirmButtonText = 'Desactivando...';

                            this.monedas.formulario.data.sisStatus = false;
                            this.editarMoneda();
                            this.limpiarDataMonedas();
                            setTimeout(() => {
                                done();
                                setTimeout(() => {
                                    instance.confirmButtonLoading = false;
                                }, 300);
                            }, 1000);
                        } else {
                            done();
                        }
                    }
                })
            }
        },
        handleInputClave(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.monedas.formulario.data.clave = value;

            if( this.monedas.formulario.dataTemporal.clave == null && this.monedas.formulario.data.clave == ''){
                this.monedas.formulario.data.clave = null
            }
        },
        handleInputDescripcion(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.monedas.formulario.data.descripcion = value;

            if( this.monedas.formulario.dataTemporal.descripcion == null && this.clasificaciones.formulario.data.descripcion == ''){
                this.monedas.formulario.data.descripcion = null
            }
        },
        // --------------------------------------------------------------------[STATUS]
        async consultarStatus(){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/status/verstatusvista/${this.user.id}`;

            try {
                this.status.table.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                if (result.status) {
                    this.status.table.data = result.data;
                    this.status.table.loading = false;
                    this.status.table.pagination.records = result.total;

                    var totalPages = Math.ceil(result.total / this.status.table.filtros.pagination.visibleRecords);
                    var pages = [];
                    if (totalPages > 0) {
                        for (var i = 0; i < totalPages; i++) {
                            pages.push({
                                value: i,
                                label: (i + 1).toString()
                            });
                        }
                    } else {
                        pages.push({
                            value: 0,
                            label: '1'
                        });
                    }
                    this.status.table.pagination.pages = pages;

                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async consultarStatu(id){
            this.status.formulario.data.id = id;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/status/verstatus/${id}/${this.user.id}`;
            try {
                this.status.formulario.loading = true;
                let res = await axios.get(url);
                var result = res.data;
                this.status.formulario.loading = false;
                if (result.status) {
                    let data = result.data[0];
                    this.status.formulario.data.id = data.id
                    this.status.formulario.data.nombre = data.nombre
                    this.status.formulario.data.tipo = data.tipo
                    this.status.formulario.data.descripcion = data.descripcion
                    this.status.formulario.data.valorExtra = data.valorExtra
                    this.status.formulario.data.valorExtra2 = data.valorExtra2
                    this.status.formulario.data.ordenamiento = data.ordenamiento
                    this.status.formulario.dataTemporal.nombre = data.nombre
                    this.status.formulario.dataTemporal.tipo = data.tipo
                    this.status.formulario.dataTemporal.descripcion = data.descripcion
                    this.status.formulario.dataTemporal.valorExtra = data.valorExtra
                    this.status.formulario.dataTemporal.valorExtra2 = data.valorExtra2
                    this.status.formulario.dataTemporal.ordenamiento = data.ordenamiento
                } else {
                    var msg = result.message;
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg });
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async registrarStatus(){
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.status.formulario.visivilidad.loadingRegistrar = true
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/status/registrar/${this.user.id}`;

            let body = {
                nombre: this.status.formulario.data.nombre,
                tipo: this.status.formulario.data.tipo,
                valorExtra: (this.status.formulario.data.valorExtra == null ? '' : this.status.formulario.data.valorExtra),
                valorExtra2: (this.status.formulario.data.valorExtra2 == null ? '' : this.status.formulario.data.valorExtra2),
                descripcion: (this.status.formulario.data.descripcion == null ? '' : this.status.formulario.data.descripcion),
            }

            try {
                let res = await axios.post(url,body);
                var result = res.data;
                this.status.formulario.loading = false;
                if (result.status) {
                    this.status.formulario.visivilidad.formulario = false
                    this.limpiarDataStatus()
                    this.updateViwSettings();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.status.formulario.visivilidad.formulario = false
                    this.status.formulario.visivilidad.loadingRegistrar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.status.formulario.visivilidad.loadingRegistrar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.status.formulario.visivilidad.loadingRegistrar = false
            }
        },
        async eliminarStatu(id){
            var urlBase = this.$axios.defaults.baseURL;
            var apiModule = this.currentProcess.procesoApiUrl;
            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/status/eliminar/${this.user.id}/${id}`;

            try {
                let res = await axios.delete(url);
                var result = res.data;
                
                if (result.status) {
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });

                    this.consultarStatus();
                } else {
                    var msg = result.message
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message :  msg});

                    instance.confirmButtonLoading = false;
                    instance.confirmButtonText = 'Aceptar';
                    done();
                }
            } catch (error) {
                console.log("Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.");
                console.log(error);
            }
        },
        async editarStatu(){
            let action = action;
            let urlBase = this.$axios.defaults.baseURL;
            let apiModule = this.currentProcess.procesoApiUrl;
            this.status.formulario.visivilidad.loadingGuardar = true

            let url = `${urlBase}:${apiModule}/ventasCatalogos/catalogos/status/editar/${this.user.id}`;
            
            let nombre = this.status.formulario.data.nombre
            nombre = (this.status.formulario.data.nombre == this.status.formulario.dataTemporal.nombre) ? null : nombre

            let tipo = this.status.formulario.data.tipo
            tipo = (this.status.formulario.data.tipo == this.status.formulario.dataTemporal.tipo) ? null : tipo

            if(this.status.formulario.visivilidad.formulario){
                if(this.status.formulario.data.nombre != this.status.formulario.dataTemporal.nombre){
                    nombre = nombre = this.status.formulario.data.nombre
                    tipo = this.status.formulario.data.tipo
                }else if(this.status.formulario.data.tipo != this.status.formulario.dataTemporal.tipo){
                    nombre = nombre = this.status.formulario.data.nombre
                    tipo = this.status.formulario.data.tipo
                }
            }

            let primerValorExtra = this.status.formulario.data.valorExtra
            primerValorExtra = (this.status.formulario.data.valorExtra == this.status.formulario.dataTemporal.valorExtra ) ? null : primerValorExtra

            let segundoValorExtra = this.status.formulario.data.valorExtra2
            segundoValorExtra = (this.status.formulario.data.valorExtra2 == this.status.formulario.dataTemporal.valorExtra2 ) ? null : segundoValorExtra

            let ordenamiento = this.status.formulario.data.ordenamiento
            ordenamiento = (this.status.formulario.data.ordenamiento == this.status.formulario.dataTemporal.ordenamiento ) ? null : parseInt(ordenamiento)

            let descripcion = this.status.formulario.data.descripcion
            descripcion = (this.status.formulario.data.descripcion == this.status.formulario.dataTemporal.descripcion ) ? null : descripcion

            let sisStatus = this.status.formulario.data.sisStatus
            sisStatus = (this.status.formulario.data.sisStatus == this.status.formulario.dataTemporal.sisStatus ) ? null : sisStatus

            let body = {
                id: this.status.formulario.data.id,
                nombre: nombre,
                tipo: tipo,
                valorExtra: primerValorExtra,
                ValorExtra2: segundoValorExtra,
                ordenamiento: ordenamiento,
                descripcion: descripcion,
                sisStatus: sisStatus,
            }

            try {
                let res = await axios.put(url,body);
                var result = res.data;
                if (result.status) {
                    if(this.status.formulario.visivilidad.formulario){
                        this.consultarStatu(result.data[0].id)
                    }
                    this.consultarStatus();
                    this.$notify({
                        message: result.message,
                        type: 'success',
                        position: 'bottom-right'
                    });
                    this.status.formulario.visivilidad.guardar = false
                    this.status.formulario.visivilidad.loadingGuardar = false
                } else {
                    var msg = result.message.split("<--->");
                    console.log(msg[1]);
                    this.$store.dispatch('getStatusAlert', { message: msg[0] });
                    this.status.formulario.visivilidad.loadingGuardar = false
                }
            } catch (error) {
                this.$store.dispatch('getStatusAlert', { message: 'Error de comunicación con el servidor, espere 10 segundos y vuelva a cargar la página y si el problema persiste contacte con el departamento de sistemas.' });
                console.log(error);
                this.status.formulario.visivilidad.loadingGuardar = false
            }
        },
        // --------------------------------------------------------------------[FUNSIONES]
        opcionesTableStatus(command){
            var action = command.action
            var data = command.data
            switch (action) {
                case 'editar':
                    this.status.formulario.visivilidad.formulario = true;
                    this.consultarStatu(data.id);
                    break;
                case 'eliminar':
                    const h = this.$createElement;
                    this.$msgbox({
                        title: 'Eliminar',
                        message: h('p', null, [
                            h('span', null, '¿Deseas eliminar este status?')
                        ]),
                        type: 'warning',
                        showCancelButton: true,
                        confirmButtonText: 'Aceptar',
                        cancelButtonText: 'Cancelar',
                        beforeClose: (action, instance, done) => {
                            if (action === 'confirm') {
                                instance.confirmButtonLoading = true;
                                instance.confirmButtonText = 'Desactivando...';
                                this.eliminarStatu(data.id);
                                setTimeout(() => {
                                    done();
                                    setTimeout(() => {
                                        instance.confirmButtonLoading = false;
                                    }, 300);
                                }, 1000);
                            } else {
                                done();
                            }
                        }
                    })
                    break;
                default:
                    this.$store.dispatch('getStatusAlert', { message :  'Esta opción no está disponible en este momento.'});
                    break;
            }
        },
        limpiarDataStatus(){
            this.status.formulario.data.id = null;
            this.status.formulario.data.nombre = null;
            this.status.formulario.data.tipo = null;
            this.status.formulario.data.valorExtra = null;
            this.status.formulario.data.valorExtra2 = null;
            this.status.formulario.data.ordenamiento = null;
            this.status.formulario.data.descripcion = null;
            this.status.formulario.data.sisStatus = null;
            this.status.formulario.dataTemporal.nombre = null;
            this.status.formulario.dataTemporal.tipo = null;
            this.status.formulario.dataTemporal.valorExtra = null;
            this.status.formulario.dataTemporal.valorExtra2 = null;
            this.status.formulario.dataTemporal.ordenamiento = null;
            this.status.formulario.dataTemporal.descripcion = null;
            this.status.formulario.dataTemporal.sisStatus = null;
        },
        validarFormularioStatus(action) {
            this.$refs["form"].validate((valid) => {
                if (valid) {
                    if (action == 'registrar') {
                        this.registrarStatus()
                    }
                    else if (action == 'editar'){
                        this.editarStatu()
                    }
                } else {
                    this.status.formulario.loading = false;
                    return false;
                }
            });
        },
        cambiarEstadoStatu(data){
            this.status.formulario.data.id = data.id
            let sisStatus = data.sisStatus

            if(sisStatus){
                this.status.formulario.data.sisStatus = true;
                this.editarStatu();
                this.limpiarDataStatus();

            }else{
                const h = this.$createElement;
                this.$msgbox({
                    title: 'Estado',
                    message: h('p', null, [
                        h('span', null, '¿Deseas desativar este status?')
                    ]),
                    type: 'warning',
                    showCancelButton: true,
                    confirmButtonText: 'Aceptar',
                    cancelButtonText: 'Cancelar',
                    beforeClose: (action, instance, done) => {
                        if (action === 'confirm') {
                            instance.confirmButtonLoading = true;
                            instance.confirmButtonText = 'Desactivando...';

                            this.status.formulario.data.sisStatus = false;
                            this.editarStatu();
                            this.limpiarDataStatus();
                            setTimeout(() => {
                                done();
                                setTimeout(() => {
                                    instance.confirmButtonLoading = false;
                                }, 300);
                            }, 1000);
                        } else {
                            done();
                        }
                    }
                })
            }
        },
        handleInputStatusNombre(value) {
        value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.status.formulario.data.nombre = value;

            if(this.status.formulario.dataTemporal.nombre == null && this.status.formulario.data.nombre == ''){
                this.status.formulario.data.nombre = null
            }
        },
        handleInputStatusTipo(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.status.formulario.data.tipo = value;

            if(this.status.formulario.dataTemporal.tipo == null && this.status.formulario.data.tipo == ''){
                this.status.formulario.data.tipo = null
            }
        },
        handleInputStatusPrimerValor(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0) + value.slice(1);
            }
            this.status.formulario.data.valorExtra = value;

            if( this.status.formulario.dataTemporal.valorExtra == null && this.status.formulario.data.valorExtra == ''){
                this.status.formulario.data.valorExtra = null
            }
        },
        handleInputStatusSegundoValor(value) {
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0) + value.slice(1);
            }
            this.status.formulario.data.valorExtra2 = value;

            if( this.status.formulario.dataTemporal.valorExtra2 == null && this.status.formulario.data.valorExtra2 == ''){
                this.status.formulario.data.valorExtra2 = null
            }
        },
        handleInputStatusOrdenamiento(value){
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0) + value.slice(1);
            }
            this.status.formulario.data.ordenamiento = value;

            if( this.status.formulario.dataTemporal.ordenamiento == null && this.status.formulario.data.ordenamiento == ''){
                this.status.formulario.data.ordenamiento = null
            }
        },
        handleInputStatusDescripcion(value){
            value = value.trimStart();
            if (value.length) {
                value = value.charAt(0).toUpperCase() + value.slice(1);
            }
            this.status.formulario.data.descripcion = value;

            if( this.status.formulario.dataTemporal.descripcion == null && this.status.formulario.data.descripcion == ''){
                this.status.formulario.data.descripcion = null
            }
        },
        // --------------------------------------------------------------------[FUNSIONES GENERALES]
        updateViwSettings() {
            var numberAttempts = 0;
            timeoutConfiguracionesLoadingPage = setInterval(() => {
                if (this.user != null && this.currentProcess != null) {
                    this.consultarConfiguraciones();
                    this.consultarClasificaciones();
                    this.consultarMonedas();
                    this.consultarStatus()
                    setTimeout(() => this.page.loaded = true, 500);
                    clearInterval(timeoutConfiguracionesLoadingPage);
                } else {
                    if (numberAttempts >= 5) {
                        console.log("problemas de conexion");
                        clearInterval(timeoutConfiguracionesLoadingPage);
                    }
                }
                numberAttempts++;
            }, 500);
        }
    },
    mounted() {
        this.user != null && this.currentProcess != null ? (
            this.consultarConfiguraciones(),
            this.consultarClasificaciones(),
            this.consultarMonedas(),
            this.consultarStatus()
        ) : this.updateViwSettings();
    },
    destroyed(){
        clearInterval(this.page.interval);
    }
}
</script>