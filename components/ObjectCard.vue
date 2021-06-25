<template>
  <section>
    <el-card class="box-card">
      <div slot="header" class="clearfix">
        <nuxt-link :to="`/objects/${object.id}`" class="object-link">
          {{ objectLinkTitle }}
        </nuxt-link>
      </div>
      <el-tabs v-model="activeName" @tab-click="handleClick">
        <el-tab-pane label="Объект" name="object">
          <el-row :gutter="10">
            <el-col>
              <div class="block">
                <el-image
                  style="width: 200px; height: 200px"
                  :src="objectPreview"
                  fit="cover"
                >
                  <div
                    slot="error"
                    class="image-error"
                    style="
                      display: flex;
                      justify-content: center;
                      align-items: center;
                      width: 100%;
                      height: 100%;
                      background: #f5f7fa;
                      color: #909399;
                    "
                  >
                    <i class="el-icon-picture-outline"></i>
                  </div>
                </el-image>
              </div>
            </el-col>
            <el-col>
              <div class="item"><span>Тип недвижимости:</span> {{ type }}</div>
              <div class="item"><span>Район:</span> {{ district }}</div>
              <div v-if="complex" class="item">
                <span>Жилищный комплекс:</span> {{ complex }}
              </div>
              <div class="item"><span>Адрес:</span> {{ address }}</div>
              <div class="item"><span>Площадь:</span> {{ area }}</div>
            </el-col>
            <el-col>
              <div class="item price">{{ price }}</div>
              <div class="item">{{ priceM }}</div>
              <div class="item">{{ floor }}</div>
              <div class="item"><span>Идентификатор:</span> {{ objectId }}</div>
            </el-col>
          </el-row>
        </el-tab-pane>
        <el-tab-pane label="Описание" name="description">
          {{ description }}
        </el-tab-pane>
        <el-tab-pane label="Фото" name="photos" class="object-photo">
          <div v-for="objectFile of objectFiles" :key='objectFile.id' class="item">
            <div v-if="objectFile" >
              <el-image :src="objectFile.uri" class="object-photo-item"></el-image>
            </div>
            <div v-show="!objectFile">
              Нет фотографий
            </div>
          </div>
        </el-tab-pane>
        <el-tab-pane label="Планировки" name="layouts">Планировки</el-tab-pane>
        <el-tab-pane label="Клиент" name="client">
          <div class="item">
            <span>Клиент: </span>
            {{ clientUsername }}
          </div>
          <div class="item">
            <span>Телефон: </span>
            <a v-if="showPhone" :href="`tel:${clientPhone}`">
              {{ clientPhone }}
            </a>
            <el-button v-else @click="showPhone = true" type="danger"
              >Показать номер</el-button
            >
          </div>
        </el-tab-pane>
        <el-tab-pane label="Агент" name="agent">
          <el-row>
            <el-col>
              <div class="item"><span>Идентификатор:</span> {{ userId }}</div>
              <div class="item"><span>ФИО:</span> {{ userName }}</div>
              <div class="item">
                <span>Телефон: </span>
                <a v-if="showPhone" :href="`tel:${userPhone}`">
                  {{ userPhone }}
                </a>
                <el-button v-else @click="showPhone = true" type="danger"
                  >Показать номер</el-button
                >
              </div>
              <div class="item">
                <span>Email: </span>
                <a :href="`mailto:${userEmail}`">{{ userEmail }}</a>
              </div>
            </el-col>
            <el-col>
              <div class="item">
                <el-image
                  style="
                    width: 200px;
                    height: 200px;
                    border-radius: 50%;
                    text-align: center;
                  "
                  :src="userPhoto"
                  fit="cover"
                >
                  <div
                    slot="error"
                    class="image-error"
                    style="
                      display: flex;
                      justify-content: center;
                      align-items: center;
                      width: 100%;
                      height: 100%;
                      background: #f5f7fa;
                      color: #909399;
                    "
                  >
                    <i class="el-icon-picture-outline"></i>
                  </div>
                </el-image>
              </div>
            </el-col>
          </el-row>
        </el-tab-pane>
        <el-tab-pane label="Менеджер" name="manager">
          <div class="item"><span>Идентификатор:</span> {{ managerId }}</div>
          <div class="item">
            <span>ФИО:</span> {{ managerName }} {{ managerSurname }}
          </div>
          <div class="item">
            <span>Телефон: </span>
            <a v-if="showPhone" :href="`tel:${managerPhone}`">
              {{ managerPhone }}
            </a>
            <el-button v-else @click="showPhone = true" type="danger"
              >Показать номер</el-button
            >
          </div>
          <div class="item">
            <span>Email: </span>
            <a :href="`mailto:${managerEmail}`">{{ managerEmail }}</a>
          </div>
        </el-tab-pane>
      </el-tabs>
    </el-card>
  </section>
</template>

<script>
const convertPrice = (labelValue) => {
  // Nine Zeroes for Billions
  return Math.abs(Number(labelValue)) >= 1.0e9
    ? (Math.abs(Number(labelValue)) / 1.0e9).toFixed(2) + " млрд"
    : // Six Zeroes for Millions
    Math.abs(Number(labelValue)) >= 1.0e6
    ? (Math.abs(Number(labelValue)) / 1.0e6).toFixed(2) + " млн"
    : // Three Zeroes for Thousands
    Math.abs(Number(labelValue)) >= 1.0e3
    ? (Math.abs(Number(labelValue)) / 1.0e3).toFixed(2) + " тыс"
    : Math.abs(Number(labelValue));
};

export default {
  data() {
    // params
    const {
      category,
      type,
      rooms,
      area,
      price,
      floor,
      floors,
      "city-district": district,
      description,
    } = this.object.params;

    // dadata
    const {
      city_with_type,
      street_with_type,
      house_type,
      block_type,
      house,
      block,
    } = this.object.params.dadata;

    // complex
    const complex = this.object.complex.title;

    // client
    const {
      name: clientName,
      surmanme: clientSurname,
      lastanme: clientLastname,
      username: clientUsername,
      phone: clientPhone,
    } = this.object.client;

    // agent
    const {
      id: userId,
      username: userName,
      phone: userPhone,
      email: userEmail,
      params: { image: userImage, image_storage: userImageStorage },
    } = this.object.user;

    const userPhoto = `https://${userImageStorage + userImage}`;

    // manager
    const {
      id: managerId,
      name: managerName,
      surname: managerSurname,
      phone: managerPhone,
      email: managerEmail,
    } = this.object.managerInfo || {};

    // object photo
    const objectFiles = this.object.files;

    const objectLinkTitle = `${rooms} комнатная, ${category}, ${area} м²`;

    const objectAddress = `${city_with_type}, ${street_with_type}, 
      ${house_type || ""} 
      ${house || ""}, 
      ${block_type || ""} 
      ${block || ""}`;

    const floorTitle = `${floor} этаж из ${floors}`;

    const priceM = Math.trunc(price / area).toLocaleString();

    const objectPreview =
      this.object.files[0] && this.object.files[0].resize.small;

    return {
      activeName: "object",
      objectLinkTitle: objectLinkTitle,
      objectId: this.object.id,
      area: `${area} м²`,
      address: objectAddress.replace(/,\s*$/, ""),
      district: district,
      price: `${convertPrice(price)} ₽`,
      priceM: `${priceM} ₽ за м²`,
      floor: floor && floorTitle,
      objectPreview: objectPreview || "",
      complex: complex,
      type: type,
      description: description,
      clientUsername: clientUsername,
      clientPhone: clientPhone,
      managerId: managerId,
      managerName: managerName,
      managerSurname: managerSurname,
      managerPhone: managerPhone,
      managerEmail: managerEmail,
      userId: userId,
      userName: userName,
      userPhone: userPhone,
      userEmail: userEmail,
      userPhoto: userPhoto || "",
      showPhone: false,
      objectFiles: objectFiles
    };
  },
  methods: {
    handleClick(tab, event) {
      console.log(tab, event);
    },
    openObject({ id }) {
      this.$router.push(`/objects/${id}`);
    },
  },
  props: ["object"],
};
</script>

<style scoped>
.text {
  font-size: 14px;
}

.item {
  margin-bottom: 18px;
}

.item > span {
  font-weight: 200;
}

.item.price {
  font-weight: 700;
}

.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}

.box-card {
  margin: 15px 0;
}

.object-link {
  text-decoration: none;
  color: #4b4791;
  font-weight: 700;
  text-transform: lowercase;
}


.object-photo {
  display: flex;
  margin: 0 -5px;
}

.object-photo-item {
  width: 200px;
  margin: 0 5px;
}


/* mobile */
@media screen and (max-width: 768px) {
  .el-row {
    display: inline-grid;
    width: 100%;
  }

  .el-image {
    width: 100% !important;
    height: auto !important;
  }

  .item .el-image {
    width: 200px !important;
    height: 200px  !important;
    display: flex;
    margin: 0 auto;
  }

  .object-photo {
    display: block;
    margin: 0 auto;
  }
  .object-photo-item {
    width: 100%;
    margin: 0 auto;
  }
}
</style>