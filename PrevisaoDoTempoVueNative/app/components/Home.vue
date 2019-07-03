<template>
  <Page class="page">
    <ActionBar class="action-bar">
      <!-- 
            Use the NavigationButton as a side-drawer button in Android
            because ActionItems are shown on the right side of the ActionBar
      -->
      <NavigationButton ios:visibility="collapsed" icon="res://menu" @tap="onDrawerButtonTap"></NavigationButton>
      <!-- 
            Use the ActionItem for IOS with position set to left. Using the
            NavigationButton as a side-drawer button in iOS is not possible,
            because its function is to always navigate back in the application.
      -->
      <ActionItem
        icon="res://navigation/menu"
        android:visibility="collapsed"
        @tap="onDrawerButtonTap"
        ios.position="left"
      ></ActionItem>
      <Label class="action-bar-title" text="Previsão do tempo"></Label>
    </ActionBar>

    <StackLayout>
      <!-- <TextField v-model="searchText" class="search-bar" /> -->

      <ListView for="item in cards" @itemTap="onItemTap">
        <v-template>
          <!-- Shows the list item label in the default color and style. -->

          <StackLayout>
            <StackLayout class="card-item">
              <Label class="text" :text="item.city" />

              <Label class="text">
                <FormattedString>
                  <Span text="Min: " />
                  <Span :text="item.min" style="color: green" />
                </FormattedString>
              </Label>
              <Label class="text">
                <FormattedString>
                  <Span text="Max: " />
                  <Span :text="item.max" style="color: orange" />
                </FormattedString>
              </Label>
              <Label class="data-att">
                <FormattedString>
                  <Span textWrap="true" text="Data da última atualização: " />
                  <Span :text="item.last" />
                </FormattedString>
              </Label>
            </StackLayout>
          </StackLayout>
        </v-template>
      </ListView>
    </StackLayout>
  </Page>
</template>

<script>
import * as utils from "~/shared/utils";
import SelectedPageService from "../shared/selected-page-service";

export default {
  mounted() {
    SelectedPageService.getInstance().updateSelectedPage("Weather");
  },
  data() {
    return {
      searchText: "",
      cards: [
        {
          city: "São Paulo",
          max: 28,
          min: 25,
          last: "02/07/2019"
        },
        {
          city: "Guarulhos",
          max: 25,
          min: 22,
          last: "02/07/2019"
        },
        {
          city: "São Bernardo",
          max: 31,
          min: 30,
          last: "02/07/2019"
        },
        {
          city: "São Caetano",
          max: 25,
          min: 21,
          last: "02/07/2019"
        },
        {
          city: "Limeira",
          max: 28,
          min: 25,
          last: "02/07/2019"
        }
      ],
    };
  },

  computed: {
    message() {
      return "<!-- Teste -->";
    },
    onItemTap() {
      console.log("deu tap");
    }
  },
  methods: {
    onDrawerButtonTap() {
      utils.showDrawer();
    },
    

    onWriteName() {}
  }
};
</script>

<style scoped lang="scss">
// Start custom common variables
@import "../app-variables";
// End custom common variables

// Custom styles
.action-bar {
  background-color: rgba(255, 255, 255, 0.4);
  color: #373564;
}

.page {
  background: rgb(32, 9, 96);
  background: linear-gradient(
    152deg,
    rgba(32, 9, 96, 1) 35%,
    rgba(120, 17, 153, 1) 100%
  );
}

.titulo {
  font-size: 20;
  color: #fff;
}

.search-bar {
  background-color: rgba(255, 255, 255, 0.3);
  border-radius: 25px;
  color: #fff;
  padding: 10;
  font-size: 20;
}

.city-title {
  color: #fff;
  margin-bottom: 30;
}

.card-item {
  margin: 40 20;
  padding: 30;
  background-color: #220a56;
  border-radius: 25px;
  border-bottom: 0;
}

.text {
  font-size: 22;
  color: #fff;
}

.data-att {
  font-size: 15;
  color: #ccc;
}
</style>
