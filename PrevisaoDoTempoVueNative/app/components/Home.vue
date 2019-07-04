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
      <TextField
        v-model="cityName"
        class="search-bar"
        @tap="cityName=''"
        @textChange="getDataByCityName"
      />

      <ListView for="item in weatherDataList" @itemTap="onItemTap">
        <v-template>
          <!-- Shows the list item label in the default color and style. -->

          <StackLayout>
            <StackLayout class="card-item">
              <Label class="city-title" :text="item.name" />
              <FlexboxLayout class="card-header">
                <Label class="temp-now" :text="item.main.temp_min + 'º'" />
                <Image src="~/assets/images/sunset.png" class="temp-icon" />
              </FlexboxLayout>

              <FlexboxLayout alignItems="center" class="temp-box">
                <Label class="text">
                  <FormattedString>
                    <Span text="Min: " />
                    <Span :text="parseInt(item.main.temp_min)" />
                  </FormattedString>
                </Label>
                <Label class="pipe"></Label>
                <Label class="text">
                  <FormattedString>
                    <Span text="Max: " />
                    <Span :text="parseInt(item.main.temp_max)" />
                  </FormattedString>
                </Label>
              </FlexboxLayout>

              <Label class="data-att" textWrap="true">
                <FormattedString>
                  <Span textWrap="true" text="Data da última atualização: " />
                  <Span :text="timeConverter(item.dt)" />
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
import * as Geolocation from "nativescript-geolocation";
import SelectedPageService from "../shared/selected-page-service";
import axios from "axios";

export default {
  created() {
    //Get Geolocation
    Geolocation.enableLocationRequest(true).then(() => {
      Geolocation.isEnabled().then(isLocationEnabled => {
        console.log("result is " + isLocationEnabled);

        if (!isLocationEnabled) {
          this.needLocation = false;
          this.locationFailure = true;
          // potentially do more then just end here...
          return;
        }

        // MUST pass empty object!!
        Geolocation.getCurrentLocation({})
          .then(result => {
            console.log("loc result", result);
            this.needLocation = false;
            this.location = result;
            this.getDataByLocation(this.location.latitude, this.location.longitude);
          })
          .catch(e => {
            console.log("loc error", e);
          });
      });
    });
  },
  data() {
    //   https://samples.openweathermap.org/data/2.5/forecast/daily?zip=94040&appid=b6907d289e10d714a6e88b30761fae22
    return {
      endPoint: "https://api.openweathermap.org/data/2.5/",
      apiKey: "785080ab68202eab8f6fd566d139edfd",
      cityName: "Buscar cidade...",
      weatherData: [],
      location: null,
      weatherDataList: [],
      searchText: ""
    };
  },
  mounted() {
    SelectedPageService.getInstance().updateSelectedPage("Weather");
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

    getDataByLocation(lat, lon) {
      axios
        .get(
          this.endPoint +
            "find?lat=" +
            lat +
            "&lon=" +
            lon +
            "&cnt=1&APPID=" +
            this.apiKey +
            "&units=metric"
        )
        .then(response => {
          this.weatherData = response.data;
          this.weatherDataList = response.data.list;
        })
        .catch(error => {
          console.log(error);
        });
    },
    getDataByCityName() {
      axios
        .get(
          this.endPoint +
            "find?q=" +
            this.cityName +
            "&cnt=1&APPID=" +
            this.apiKey +
            "&units=metric"
        )
        .then(response => {
          this.weatherData = response.data;
          this.weatherDataList = response.data.list;
          this.validCity = true;
        })
        .catch(error => {
          console.log(error);
        });
    },
    timeConverter(UNIX_timestamp) {
      var a = new Date(UNIX_timestamp * 1000);
      var months = [
        "Jan",
        "Feb",
        "Mar",
        "Apr",
        "May",
        "Jun",
        "Jul",
        "Aug",
        "Sep",
        "Oct",
        "Nov",
        "Dec"
      ];
      var year = a.getFullYear();
      var month = months[a.getMonth()];
      var date = a.getDate();
      var hour = a.getHours();
      var min = a.getMinutes();
      var sec = a.getSeconds();
      var time = date + " de " + month + " de " + year + " " + hour + ":" + min;
      return time;
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

  .title {
    font-size: 20;
    color: #fff;
  }

  .search-bar {
    background-color: rgba(255, 255, 255, 0.9);
    border-radius: 50px;
    color: #373564;
    padding: 10;
    font-size: 20;
    margin: 20;
    text-align: center;
    width: 90%;
  }

  .card-item {
    margin: 40 20;
    padding: 30;
    background-color: #220a56;
    border-radius: 25px;
    border-bottom: 0;

    .card-header {
      justify-content: space-between;
      align-items: center;
      margin-bottom: 50;

      .temp-now {
        font-size: 50;
        color: #fff;
      }

      .temp-icon {
        width: 120;
      }
    }

    .city-title {
      color: #fff;
      margin-bottom: 30;
      font-size: 30;
    }

    .temp-box {
      justify-content: space-around;
      margin-bottom: 50;
      padding: 0 50;

      .pipe {
        width: 1px;
        height: 150px;
        background-color: #fff;
      }
    }
    .data-att {
      font-size: 15;
      color: #ccc;
    }
  }

  .text {
    font-size: 18;
    color: #fff;
  }
}
</style>
