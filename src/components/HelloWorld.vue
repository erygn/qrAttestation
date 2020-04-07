<template>
  <v-container fluid>
    <v-row justify="center">
      <div class="col-12 text-center">
        <h2 style="margin-top: 40px" class="text-uppercase mb-3">
          Générateur d'attestation de déplacement dérogatoire
        </h2>

        <p class="subheading font-weight-regular">
          Généner vos attestastion de déplacement pour le covid-19 plus rapidement
        </p>

        <v-row justify="center">
          <div class="col-lg-4 col-md-5 col-sm-12 text-center mt-10">
            <h2 class="headline font-weight-bold mb-3">
              Vos informations
            </h2>
            <v-text-field color="#373737" clearable outlined label="Nom" type="text" class="subheading mx-3" v-model="name" />
            <v-text-field color="#373737" clearable outlined label="Prenom" type="text" class="subheading mx-3" v-model="surname" />
            <v-text-field color="#373737" clearable outlined label="Date de Naissance" type="date" class="subheading mx-3" v-model="birthday" />
            <v-text-field color="#373737" clearable outlined label="Lieu de Naissance" type="text" class="subheading mx-3" v-model="zoneBirth" />
            <v-text-field color="#373737" clearable outlined label="Adresse" type="text" class="subheading mx-3" v-model="address" />
            <v-text-field color="#373737" clearable outlined label="Ville" type="text" class="subheading mx-3" v-model="city" />
            <v-text-field color="#373737" clearable outlined label="Code Postal" type="number" min="0" class="subheading mx-3" v-model="postalCode" />
          </div>
        </v-row>

        <v-row justify="center">
          <div class="col-lg-4 col-md-5 col-sm-12 text-center mt-10">
            <h2 class="headline font-weight-bold mb-3">
              Choissisez un motif de sortie
            </h2>
            <v-checkbox value="1" v-model="travail" label="Déplacements entre le domicile et le lieu d’exercice de l’activité professionnelle, lorsqu'ils sont indispensables à l'exercice d’activités ne pouvant être organisées sous forme de télétravail ou déplacements professionnels ne pouvant être différés."></v-checkbox>
            <v-checkbox value="1" v-model="courses" label="Déplacements pour effectuer des achats de fournitures nécessaires à l’activité professionnelle et des achats de première nécessité dans des établissements dont les activités demeurent autorisées"></v-checkbox>
            <v-checkbox value="1" v-model="sante" label="Consultations et soins ne pouvant être assurés à distance et ne pouvant être différés ; consultations et soins des patients atteints d'une affection de longue durée."></v-checkbox>
            <v-checkbox value="1" v-model="famille" label="Déplacements pour motif familial impérieux, pour l’assistance aux personnes vulnérables ou la garde d’enfants."></v-checkbox>
            <v-checkbox value="1" v-model="sport" label="Déplacements brefs, dans la limite d'une heure quotidienne et dans un rayon maximal d'un kilomètre autour du domicile, liés soit à l'activité physique individuelle des personnes, à l'exclusion de toute pratique sportive collective et de toute proximité avec d'autres personnes, soit à la promenade avec les seules personnes regroupées dans un même domicile, soit aux besoins des animaux de compagnie."></v-checkbox>
            <v-checkbox value="1" v-model="judiciaire" label="Convocation judiciaire ou administrative."></v-checkbox>
            <v-checkbox value="1" v-model="missions" label="Participation à des missions d’intérêt général sur demande de l’autorité administrative."></v-checkbox>

            <v-text-field outlined label="Date de sortie" type="date" class="subheading mx-3 mt-5" v-model="leaveDate" />
            <v-text-field outlined label="Heure de sortie" type="time" class="subheading mx-3" v-model="leaveHour" />
          </div>
        </v-row>

        <v-row justify="center">
          <div class="col-lg-4 col-md-5 col-sm-12 text-center">
            <v-btn color="primary" height="50px" @click="generateAttest">Générer mon attestation</v-btn>
          </div>
        </v-row>
      </div>
    </v-row>

    <v-row>
      <div class="col-12">
        <div ref="content">
          <h1>Attestation de déplacement</h1>
          <qrcode-vue  :value="value" :size="size" level="H"></qrcode-vue>
        </div>
      </div>
    </v-row>
  </v-container>
</template>

<script>
  import QrcodeVue from 'qrcode.vue';
  import jspdf from 'jspdf';
  import domtoimage from 'dom-to-image'

  const STORAGE_KEY_NAME = 'attName';
  const STORAGE_KEY_SURNAME = 'attSurname';
  const STORAGE_KEY_BIRTHDATE = 'attBirthDate';
  const STORAGE_KEY_BIRTHZONE = 'attBirthZone';
  const STORAGE_KEY_ADDRESS = 'attAddress';
  const STORAGE_KEY_CITY = 'attCity';
  const STORAGE_KEY_POSTALCODE = 'attPostalCode';
  export default {
    name: 'HelloWorld',
    components: {
      QrcodeVue
    },
    methods: {
      generateAttest: function () {
          localStorage.setItem(STORAGE_KEY_NAME, JSON.stringify(this.name))
          localStorage.setItem(STORAGE_KEY_SURNAME, JSON.stringify(this.surname))
          localStorage.setItem(STORAGE_KEY_BIRTHDATE, JSON.stringify(this.birthday))
        let year = this.birthday.substring(0, 4)
        let day = this.birthday.substring(8, 10)
        let mouth = this.birthday.substring(5, 7)
        this.birthFinal = day + '/' + mouth + '/' + year
          localStorage.setItem(STORAGE_KEY_BIRTHZONE, JSON.stringify(this.zoneBirth))
          localStorage.setItem(STORAGE_KEY_ADDRESS, JSON.stringify(this.address))
          localStorage.setItem(STORAGE_KEY_CITY, JSON.stringify(this.city))
          localStorage.setItem(STORAGE_KEY_POSTALCODE, JSON.stringify(this.postalCode))

        let Lyear = this.leaveDate.substring(0, 4);
        let Lday = this.leaveDate.substring(8, 10);
        let Lmouth = this.leaveDate.substring(5, 7);
        this.leaveDateFinal = Lday + '/' + Lmouth + '/' + Lyear;

        let hour = this.leaveHour.substring(0, 2);
        let min = this.leaveHour.substring(3, 5);
        this.leaveHourFinal = hour + 'h' + min;

        if (this.travail == 1) {
          if (this.motif == null) {
            this.motif = 'travail';
          } else {
            this.motif += '-travail';
          }
        }
        if (this.courses == 1) {
          if (this.motif == null) {
            this.motif = 'courses';
          } else {
            this.motif += '-courses';
          }
        }
        if (this.sport == 1) {
          if (this.motif == null) {
            this.motif = 'sport';
          } else {
            this.motif += '-sport';
          }
        }
        if (this.sante == 1) {
          if (this.motif == null) {
            this.motif = 'sante';
          } else {
            this.motif += '-sante';
          }
        }
        if (this.famille == 1) {
          if (this.motif == null) {
            this.motif = 'famille';
          } else {
            this.motif += '-famille';
          }
        }
        if (this.judiciaire == 1) {
          if (this.motif == null) {
            this.motif = 'judiciaire';
          } else {
            this.motif += '-judiciaire';
          }
        }
        if (this.missions == 1) {
          if (this.motif == null) {
            this.motif = 'missions';
          } else {
            this.motif += '-missions';
          }
        }


        this.value = 'Cree le: ' + new Date().toLocaleString('fr-FR').substring(0, 10) + ' a ' + new Date().toLocaleString('fr-FR').substring(13, 15) + 'h' + new Date().toLocaleString('fr-FR').substring(16, 18) + '; Nom: ' + this.name + '; Prenom: ' + this.surname + '; Naissance: ' + this.birthFinal + ' a ' + this.zoneBirth + '; Adresse: ' + this.address + ' ' + this.postalCode + ' ' + this.city + '; Sortie: ' + this.leaveDateFinal + ' a ' + this.leaveHourFinal + '; Motifs: ' + this.motif;
        this.createPDF();
      },
      createPDF() {
        // const doc = new jspdf();
        // const contentHTML = this.$refs.content.innerHTML;
        // const imgData = 'data:image/png;base64,' + Base64.encode();
        // doc.fromHTML(contentHTML, 15, 15, {
        //   width: 170
        // })
        // // doc.text("HelloWorld", 15, 15);
        // doc.save("det.pdf");


        domtoimage
                .toPng(this.$refs.content)
                .then(function(dataUrl) {
                  var img = new Image();
                  img.src = dataUrl;
                  const doc = new jspdf({
                    orientation: "portrait",
                    // unit: "pt",
                    format: [900, 1400]
                  });
                  doc.addImage(img, "JPEG", 20, 20);
                  doc.save('attestation.pdf');
                })
                .catch(function(error) {
                  console.error("oops, something went wrong!", error);
                });
      }
    },
    data: () => ({
      size: 300,
      name: null,
      surname: null,
      birthday: null,
      birthFinal: null,
      zoneBirth: null,
      address: null,
      city: null,
      postalCode: null,

      value: null,

      leaveDate: null,
      leaveDateFinal: null,
      leaveHour: null,
      leaveHourFinal: null,

      travail: null,
      sport: null,
      courses: null,
      sante: null,
      famille: null,
      judiciaire: null,
      missions: null,

      motif: null,
      motifs: ['sport', 'travail', 'courses', 'sante', 'famille', 'judiciaire', 'missions']
    }),
    created() {
      let today = new Date().toLocaleString();

      this.leaveDate = today.substring(6, 10) + '-' + today.substring(3, 5) + '-' + today.substring(0, 2);
      this.leaveHour = today.substring(13, 15) + ':' + today.substring(16, 18);
      this.name = JSON.parse(localStorage.getItem(STORAGE_KEY_NAME || null));
      this.surname = JSON.parse(localStorage.getItem(STORAGE_KEY_SURNAME || null));
      this.birthday = JSON.parse(localStorage.getItem(STORAGE_KEY_BIRTHDATE || null));
      this.zoneBirth = JSON.parse(localStorage.getItem(STORAGE_KEY_BIRTHZONE || null));
      this.address = JSON.parse(localStorage.getItem(STORAGE_KEY_ADDRESS || null));
      this.city = JSON.parse(localStorage.getItem(STORAGE_KEY_CITY || null));
      this.postalCode = JSON.parse(localStorage.getItem(STORAGE_KEY_POSTALCODE || null));
    }
  }
</script>
