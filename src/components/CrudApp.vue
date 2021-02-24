<template>
    <div style="margin: 0 auto; width: 80%">
        <Toast />
        <Panel header="Lista de Personas">
            <Menubar :model="items" />
            <DataTable :value="personas" :paginator="true"  :selection.sync="selectedPersona" selectionMode="single" :rows="10" dataKey="id">
                <Column field="id" header="Id"></Column>
                <Column field="nombre" header="Nome"></Column>
                <Column field="apellido" header="Apelido"></Column>
                <Column field="direccion" header="Direção"></Column>
                <Column field="telefono" header="Telefone"></Column>
            </DataTable>
        </Panel>
        <Dialog header="Criar Persona" :visible.sync="displayModal" :modal="true">
            <br>
            <span class="p-float-label">
                <InputText id="nombre" type="text" v-model="persona.nombre" style="width: 100%"/>
                <label for="nombre">Nome</label>
            </span>
            <br>
            <span class="p-float-label">
                <InputText id="apellido" type="text" v-model="persona.apellido" style="width: 100%" />
                <label for="apellido">Apelido</label>
            </span>
            <br>
            <span class="p-float-label">
                <InputText id="direccion" type="text" v-model="persona.direccion" style="width: 100%"/>
                <label for="direccion">Direção</label>
            </span>
            <br>
            <span class="p-float-label">
                <InputText id="telefono" type="text" v-model="persona.telefono" style="width: 100%" />
                <label for="telefono">Telefone</label>
            </span>
            <br>
            <template #footer>
                <Button label="Cancelar" icon="pi pi-times" @click="closeModal" class="p-button-text"/>
                <Button label="Salvar" icon="pi pi-check" @click="save" autofocus />
            </template>
        </Dialog>
        <Dialog header="Confirmação" :visible.sync="displayConfirmation" :style="{width: '350px'}" :modal="true">
            <div class="confirmation-content">
                <i class="pi pi-exclamation-triangle p-mr-3" style="font-size: 2rem" />
                <span>Deseja realmente excluir este registro?</span>
            </div>
            <template #footer>
                <Button label="Não" icon="pi pi-times" @click="closeConfirmation" class="p-button-text"/>
                <Button label="Sim" icon="pi pi-check" @click="removePersona" class="p-button-text" autofocus />
            </template>
        </Dialog>        
    </div>
</template>

<script>
import PersonaService from '../service/PersonaService';
export default {
    name : 'CrudApp',
    data() {
        return {
            personas : null,
            persona: {
                id : null,
                nombre : null,
                appelido : null,
                direccion : null,
                telefono : null
            },
            selectedPersona : {
                id: null,
                nombre : null,
                appelido : null,
                direccion : null,
                telefono : null
            },
            items : [
                {
                    label: 'Novo',
                    icon: 'pi pi-fw pi-plus',
                    command : () => {
                       this.showSaveModal();         
                    }
                },
                {
                    label : 'Editar',
                    icon: 'pi pi-fw pi-pencil',
                    command : () => {
                        this.showEditModal()
                    }
                },
                {
                    label : 'Excluir',
                    icon: 'pi pi-fw pi-trash',
                    command : () => {
                       this.showDeleteModal();     
                    }
                },
                {
                    label : 'Atualizar',
                    icon: 'pi pi-fw pi-refresh',
                    command : () => {
                        this.getAll();
                    }
                },
            ],
            displayModal : false,
            displayConfirmation : false
        }
    },
    personaService : null,
    created() {
        this.personaService = new PersonaService();
    },
    mounted() {
       this.getAll();
    },
    methods : {
        showSaveModal() {
            this.displayModal = true;
        },
        showDeleteModal() {
            this.displayConfirmation = true;
        },
        closeConfirmation() {
            this.displayConfirmation = false;
        },
        getAll() {
            this.personaService.getAll().then(data => {
                this.personas = data.data;
            });
        },
        removePersona(){
            this.displayConfirmation = false;
            this.personaService.delete(this.selectedPersona.id).then(data => {
                if(data.status === 200) {
                    this.$toast.add({severity:'success', summary: 'Sucesso!', detail:'Persona selecionada foi excluída corretamente', life: 3000});
                    this.getAll();
               }
            });
        },
        save() {
           this.personaService.save(this.persona).then(data => {
               if(data.status === 200) {
                   this.displayModal = false;
                   this.persona = {
                        nombre : null,
                        appelido : null,
                        direccion : null,
                        telefono : null
                    }
                   this.getAll();
               }
           });     
        },
        closeModal() {
            this.displayModal = false;
        },
        showEditModal() {
            this.persona = this.selectedPersona
            this.displayModal = true;
        }
    }
}
</script>

<style>

</style>