<template>
  <v-app>
    <v-app-bar flat class="border-b">
      <v-app-bar-nav-icon @click="isDrawerOpen = !isDrawerOpen" class="d-md-none">
      </v-app-bar-nav-icon>
      <v-app-bar-title>PlayTogether</v-app-bar-title>

      <template #append>
        <v-btn class="d-none d-md-flex">Entrar</v-btn>
        <v-btn class="d-none d-md-flex" color="primary" variant="tonal">Cadastrar</v-btn>
      </template>
    </v-app-bar>

    <v-navigation-drawer v-model="isDrawerOpen" temporary class="d-md-none">
      <v-container class="d-flex flex-column justify-space-around">
        <v-btn class="mb-4"> Entrar</v-btn>
        <v-btn class="mb-4" color="primary" variant="tonal">Cadastrar</v-btn>
      </v-container>
    </v-navigation-drawer>

    <v-main>
      <v-container class="border" :max-width="smAndDown ? '100%' : '60%'">
        <v-row class="my-4 justify-center">
          <v-col cols="12">
            <v-text-field v-model="searchQuery" label="Buscar" append-inner-icon="mdi-magnify" rounded="xl"
              variant="outlined" flat hide-details single-line class="search-field"></v-text-field>
          </v-col>
        </v-row>
        <v-row class="my-4 flex-nowrap overflow-x-auto remove-scroll-bar pt-4" no-gutters>

          <v-col cols="8" sm="4" md="4" class="px-1 horizontal-filter">
            <v-autocomplete v-model="selectedCity" :items="cities" label="Cidade/Bairro" variant="outlined" clearable
              dense hide-no-data hide-selected prepend-inner-icon="mdi-city" class="filter-field"
              rounded="xl"></v-autocomplete>
          </v-col>

          <v-col cols="8" sm="4" md="4" class="px-1 flex-shrink-0 horizontal-filter">
            <v-select v-model="selectedSport" :items="sports" label="Modalidades" variant="outlined" clearable dense
              prepend-inner-icon="mdi-basketball" class="filter-field" rounded="xl"></v-select>
          </v-col>

          <v-col cols="8" sm="4" md="4" class="px-1 flex-shrink-0 horizontal-filter">
            <v-date-input v-model="selectedDate" label="Data" variant="outlined" prepend-inner-icon="mdi-calendar"
              prepend-icon="" color="primary" clearable dense class="filter-field" rounded="xl"></v-date-input>
          </v-col>
        </v-row>

        <v-row>
          <v-col>
            <v-tabs v-model="activeTab" background-color="primary" dark grow class="my-4">
              <v-tab v-for="tab in tabs" :key="tab">
                {{ tab }}
              </v-tab>
            </v-tabs>

            <!-- Conteúdo das Abas -->
            <v-tabs-items v-model="activeTab">
              <!-- Aba de Eventos -->
              <v-tab-item>
                <v-container v-if="activeTab === 0">
                  <v-row>
                    <v-col v-for="event in filteredEvents" :key="event.name" cols="12">
                      <v-card outlined class="hover-card">
                        <v-row no-gutters>
                          <!-- Coluna da Imagem -->
                          <v-col cols="4" sm="2" class="d-flex align-center justify-center">
                            <v-img :src="logo" aspect-ratio="1" class="ma-2" contain alt="Logo do Evento"></v-img>
                          </v-col>

                          <!-- Coluna do Conteúdo -->
                          <v-col cols="6" sm="8">
                            <v-card-title class="headline">{{ event.name }}</v-card-title>
                            <v-card-subtitle class="grey--text">{{ formatDate(event.date) }}</v-card-subtitle>
                            <v-card-text>{{ event.description }}</v-card-text>
                          </v-col>

                          <!-- Coluna do Botão -->
                          <v-col cols="2" sm="2" class="d-flex align-center justify-end">
                            <v-btn color="primary" @click="solicitar(event)" elevation="2"
                              class="mr-4 d-none d-sm-flex">
                              Solicitar
                            </v-btn>
                          </v-col>
                        </v-row>
                      </v-card>
                    </v-col>
                  </v-row>
                </v-container>
              </v-tab-item>

              <!-- Aba de Times -->
              <v-tab-item>
                <v-container v-if="activeTab === 1">
                  <v-row>
                    <v-col v-for="team in filteredTeams" :key="team.name" cols="12">
                      <v-card outlined class="hover-card">
                        <v-card-title>{{ team.name }}</v-card-title>
                        <v-card-subtitle>{{ team.sport }}</v-card-subtitle>
                        <v-card-text>{{ team.description }}</v-card-text>
                      </v-card>
                    </v-col>
                  </v-row>
                </v-container>
              </v-tab-item>
            </v-tabs-items>

          </v-col>
        </v-row>

      </v-container>
    </v-main>
  </v-app>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';
import logo from '@/assets/logo.png';

import { useDisplay } from 'vuetify';
import { VDateInput } from 'vuetify/labs/VDateInput'

const { smAndDown } = useDisplay();
const isDrawerOpen = ref(false);

const searchQuery = ref(' ');
const tabs = ['Eventos', 'Times'];

const activeTab = ref(0);

//Filtros
// const maxDate = ref('2030-12-31');
// const selectedDate = ref(null);

const selectedCity = ref(null);
const cities = [
  'São Paulo',
  'Rio de Janeiro',
  'Belo Horizonte',
  'Curitiba',
  'Porto Alegre',
  'Salvador',
];

const selectedSport = ref(null);
const sports = [
  'Futebol',
  'Basquete',
  'Vôlei',
  'Tênis',
  'Natação',
  'Atletismo',
];

// Dados de exemplo para times e eventos
const teams = [
  { name: 'Time A', sport: 'Futebol', description: 'Descrição do Time A', city: 'São Paulo' },
  { name: 'Time B', sport: 'Basquete', description: 'Descrição do Time B', city: 'Rio de Janeiro' },
  { name: 'Time C', sport: 'Vôlei', description: 'Descrição do Time C', city: 'Belo Horizonte' },
];

const events = [
  { name: 'Evento 1', date: new Date('2024-09-15'), description: 'Descrição do Evento 1', city: 'São Paulo', sport: 'Futebol' },
  { name: 'Evento 2', date: new Date('2024-10-01'), description: 'Descrição do Evento 2', city: 'Rio de Janeiro', sport: 'Basquete' },
  { name: 'Evento 3', date: new Date('2024-11-20'), description: 'Descrição do Evento 3', city: 'Belo Horizonte', sport: 'Vôlei' },
  { name: 'Evento 4', date: new Date('2024-12-05'), description: 'Descrição do Evento 4', city: 'Curitiba', sport: 'Tênis' },
];
const selectedDate = ref(null);

const solicitar = (event: { name: any; }) => {
  // Lógica para solicitar o evento
  // Por exemplo, redirecionar para uma página de solicitação ou abrir um modal
  console.log(`Solicitando o evento: ${event.name}`);
};

// Função para formatar datas
const formatDate = (dateStr: string | number | Date) => {
  return new Date(dateStr).toLocaleDateString();
};

// Computed para filtrar eventos
const filteredEvents = computed(() => {
  return events.filter(event => {
    debugger
    const matchesCity = selectedCity.value ? event.city === selectedCity.value : true;
    const matchesSport = selectedSport.value ? event.sport === selectedSport.value : true;
    const matchesDate = selectedDate.value ? event.date.toLocaleDateString() === new Date(selectedDate.value).toLocaleDateString() : true;
    const matchesSearch = searchQuery.value
      ? event.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      event.description.toLowerCase().includes(searchQuery.value.toLowerCase())
      : true;
    return matchesCity && matchesSport && matchesDate && matchesSearch;
  });
});


// Computed para filtrar times
const filteredTeams = computed(() => {
  return teams.filter(team => {
    const matchesCity = selectedCity.value ? team.city === selectedCity.value : true;
    const matchesSport = selectedSport.value ? team.sport === selectedSport.value : true;
    const matchesSearch = searchQuery.value
      ? team.name.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
        team.description.toLowerCase().includes(searchQuery.value.toLowerCase())
      : true;
    return matchesCity && matchesSport && matchesSearch;
  });
});

</script>

<style scoped>
/* Hide scrollbar for Chrome, Safari and Opera */
.remove-scroll-bar::-webkit-scrollbar {
  display: none;
}

/* Hide scrollbar for IE, Edge and Firefox */
.remove-scroll-bar {
  -ms-overflow-style: none;
  /* IE and Edge */
  scrollbar-width: none;
  /* Firefox */
}

.horizontal-filter {
  min-width: 150px;
}
</style>
