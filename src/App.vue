<template>
  <div class="game-maker" v-cloak>
    <div class="container text-white pt-5">
      <h3 class="mb-3">表示エリア</h3>
      <div class="display d-md-flex justify-content-between align-items-center bg-dark p-3 mb-5">
        <div class="chara-img col-12 col-md-3 p-3 border-white mb-3 mb-md-0">
          <img class="img-fit" :src="matched[0].imgSrc" alt="">
        </div>
        <div class="info col-12 col-md-9 pr-0 pl-0 pl-md-3">
          <div class="info-basic border-white mb-3">
            <ul>
              <li>Name : {{userData.name}}</li>
              <li>Class : {{jobs[userData.selectedJob]}}</li>
              <li>Gender : {{userData.picked}}</li>
              <li>Traits : {{userData.selectedTrait}}</li>
            </ul>
          </div>
          <div class="status border-white">
            <ul>
              <li>Strength : {{calculateStatus(userData.selectedTrait).strength}}</li>
              <li>Agility : {{calculateStatus(userData.selectedTrait).agility}}</li>
              <li>Resilience : {{calculateStatus(userData.selectedTrait).resilience}}</li>
              <li>Wisdom : {{calculateStatus(userData.selectedTrait).wisdom}}</li>
              <li>Luck : {{calculateStatus(userData.selectedTrait).luck}}</li>
            </ul>
          </div>
        </div>
      </div>
      <div class="input-area py-5 px-2">
        <h3 class="mb-3">入力エリア</h3>
        <div class="form-group mb-3 d-flex">
          <label class="py-2 pr-2 pl-0 col-md-1">Name</label>
          <input type="text" class="col-10 col-md-3 form-control" v-model="userData.name">
        </div>
        <div class="form-group mb-3 d-flex">
          <label class="form-check-label col-md-1 py-2 pr-2 pl-0" for="male">Gender</label>
          <div class="col-10 col-md-3 d-flex align-items-center px-0">
            <div class="mr-3">
              <input type="radio" value="Male" name="gender" id="male" v-model="userData.picked">
              <label class="form-check-label" for="female">Male</label>
            </div>
            <div>
              <input type="radio" value="Female" name="gender" id="female" v-model="userData.picked">
              <label class="form-check-label">Female</label>
            </div>
          </div>
        </div>
        <div class="form-group mb-3 d-flex">
          <label class="py-2 pr-2 col-md-1 pl-0">Class</label>
          <select class="form-select col-10 col-md-3 form-control" v-model="userData.selectedJob">
            <option v-for="(job,index) in jobs" :key="index" :value="index">{{job}}</option>
          </select>
        </div>
        <div class="form-group mb-3 d-flex">
          <label class="py-2 pr-2 col-md-1 pl-0">Traits</label>
          <select class="form-select col-10 col-md-3 form-control" v-model="userData.selectedTrait" @change="calculateStatus(userData.selectedTrait)">
            <option v-for="(trait,index) in traitRegulation" :key="index" :value="trait">{{trait}}</option>
          </select>
        </div>
      </div>
    </div>
  </div>
</template>
<script lang="ts" setup>
import { reactive, computed } from 'vue'
import characters from './characters';
const traits = ["everyman","bat out of hell","brave","lucky devil","tomboy"];
const jobs = ["hero","warrior","mage"];
type Status = {
  [strength: string]: number;
}
type UserData = {
  name: string;
  jobs: string[];
  traits: string[];
  characters: Character[];
  selectedJob: number;
  selectedTrait: String;
  picked: string;
  initialStatusValue: number;
  status: Status;
}
const userData = reactive<UserData>({
  name : "Unknown",
  jobs : jobs,
  traits : traits,
  characters : characters,
  selectedJob : 0,
  selectedTrait : traits[0],
  picked : "Male",
  initialStatusValue : 10,
  status : {
    strength : 10,
    agility : 10,
    resilience : 10,
    wisdom : 10,
    luck : 10,
  }
})
const calculateStatus = (trait: String) => {
  switch(trait){
    case "everyman" :
      for(let key in userData.status){
        userData.status[key] = userData.initialStatusValue;
      }
      break;
    case "bat out of hell" :
      for(let key in userData.status){
        if(key === "agility"){
          userData.status[key] = userData.initialStatusValue * 1.4;
        }else{
          userData.status[key] = userData.initialStatusValue
        }
      }
      break;
    case "brave" :
      for(let key in userData.status){
        if(key === "strength" || key === "agility"){
          userData.status[key] = userData.initialStatusValue * 1.1;
        }else if(key === "luck"){
          userData.status[key] = userData.initialStatusValue * 1.2;
        }else{
          userData.status[key] = userData.initialStatusValue
        }
      }
      break;
    case "lucky devil" :
      for(let key in userData.status){
        if(key === "luck"){
          userData.status[key] = userData.initialStatusValue * 1.5;
        }else{
          userData.status[key] = userData.initialStatusValue
        }
      }
      break;
    case "tomboy" :
      for(let key in userData.status){
        if(key === "strength" || key === "agility"){
          userData.status[key] = userData.initialStatusValue * 1.1;
        }else{
          userData.status[key] = userData.initialStatusValue
        }
      }
      break;
  }
  return userData.status;
}
const matched = computed(() => {
  let results = [];
  for(let i = 0; i < characters.length; i++){
    let result = characters[i];
    if(result.job === jobs[userData.selectedJob] && result.gender === userData.picked){
      results.push(result);
    }
  }
  return results;
})
const traitRegulation = computed(() => {
  let traitsResult = [];
  traitsResult.push(traits[0],traits[1]);
  if(userData.picked === "Male"){
    traitsResult.push(traits[3]);
  }else{
    traitsResult.push(traits[4]);
  }
  if(userData.selectedJob === 0){
    traitsResult.splice(2,0,traits[2]);
  }
  return traitsResult;
})
</script>
<style scoped>
.game-maker {
  font-family: National2CompressedWeb,"Helvetica Condensed","Arial Narrow",sans-serif;
  letter-spacing: .05rem;
  background: #326826;
  font-size: 1.3rem;
}

.game-maker p {
  margin: 0;
}
img{
  width: 100%;
  max-width: 100%;
  height: auto;
}
.img-fit {
  object-fit: contain;
}
[v-cloak] { display: none; }
li{
  list-style: none;
}
ul{
  margin: 0;
}
.display{
  border-radius: 5px;
}
.border-white{
  border: 3px solid white;
  border-radius: 5px;
}
.input-area{
  margin-right: auto;
  font-size: 1.0rem;
}
</style>
