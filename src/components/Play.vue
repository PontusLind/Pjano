<script>
import Pjano from './Pjano.vue';
import Question from './Question.vue';
import Info from './Info.vue';
import lessonsData from '../../assets/Lessones.json';
import User from '../models/User';
import Conter from './Conter.vue';

export default {
  components: {
    Pjano,
    Question,
    Info,
    Conter

  },

  data() {
    return {
      clickedNote: [],
      lessonID: this.$route.params.id,
      note: lessonsData.lessons[this.$route.params.id - 1].chords[0][0],
      chordName: lessonsData.lessons[this.$route.params.id - 1].chordname
        ? lessonsData.lessons[this.$route.params.id - 1].chordname[0]
        : null,
      answer: '',
      user: User.loadFromLocalStorage(),
      i: 1,
      x: 0,
      backgroundColor: '#f5f5f5',
      checked: false,
      tryAgainBtn: null,
      freePlay: this.$route.params.id === '6',
      questionsWidth: 0,
      showNotesWidth: 0,
      questionCheckboxHeight: 0,
    }
  },

  created() {
    console.log(this.$route.params.id)
    console.log(lessonsData)
    console.log(lessonsData.lessons[this.$route.params.id - 1].chords[this.i - 1])
    if (this.user.progress[this.lessonID] !== undefined) {
      this.user.progress[this.lessonID] += 25;
    }
    else {
      this.user.progress[this.lessonID] = 0;
    }
    this.user.saveToLocalStorage();
  },

  methods: {
    emittedTone(value) {
      this.clickedNote.push(value)
      console.log("du tryckte på:", value)
      if (value === this.note) {
        console.log("rätt")
        this.answer = 'Well done!'
        this.nextLesson()
        this.backgroundColor = '#f5f5f5'
      } else {
        console.log("fel")
        this.answer = 'Try again'
        this.backgroundColor = 'red'
      }
    },
    // nextLesson(){
    //   const lesson= lessonsData.lessons[this.$route.params.id -1]

    //   if (lesson.chordname && lesson.chordname[this.x]){
    //     this.chordName = lesson.chordname[this.x]
    //   } else{
    //     this.chordName = null
    //   }
    //   if(this.x<lesson.chords.length){
    //     if(this.i<lesson.chords[this.x].length){
    //       this.note = lesson.chords[this.x][this.i]
    //       this.i++
    //     } else {
    //       this.i =0
    //       this.x++
    //       if(lesson.chordname && lesson.chordname[this.x]){
    //         this.chordName = lesson.chordname[this.x]
    //       } else {
    //         this.chordName =null
    //       }
    //       // this.chordName = lesson.chordname[this.x]
    //       // lessonsData.lessons[this.$route.params.id - 1].chordname[this.x]
    //       if(this.x >= lesson.chords.length){
    //         this.note = lesson.chords[this.x][this.i]
    //         this.i++
    //       } else {
    //         this.answer = 'Lesson finished'
    //         this.tryAgainBtn = true
    //         // this.chordName=null
    //       }
    //     }
    //   }
    // }
    nextLesson() {
      {
        if (this.x < lessonsData.lessons[this.$route.params.id - 1].chords.length) {
          if (this.chordName !== null) { this.chordName = lessonsData.lessons[this.$route.params.id - 1].chordname[this.x] }


          if (this.i < lessonsData.lessons[this.$route.params.id - 1].chords[this.x].length) {
            this.note = lessonsData.lessons[this.$route.params.id - 1].chords[this.x][this.i]
            this.i++

          } else {
            this.i = 0
            this.x++
            if (this.chordName !== null) { this.chordName = lessonsData.lessons[this.$route.params.id - 1].chordname[this.x] }

            if (this.x >= lessonsData.lessons[this.$route.params.id - 1].chords.length) {
              this.answer = 'Lesson finished'
              this.tryAgainBtn = true

              let lessonsCount = parseInt(localStorage.getItem('lessonsCount') || '0')
              lessonsCount += 1
              localStorage.setItem('lessonsCount', lessonsCount)
              console.log("Lessons count: ", lessonsCount)

              this.chordName = null



            }
            else {
              this.note = lessonsData.lessons[this.$route.params.id - 1].chords[this.x][this.i]
              this.i++
            }
          }

        }
      }
    },
    calculateKeysSize() {
      this.screenWidth = window.innerWidth < 1000 ? window.innerWidth : 1000;
      this.questionsWidth = this.screenWidth * 0.8;
      this.showNotesWidth = this.screenWidth * 0.2;
      this.questionCheckboxHeight = this.screenWidth * 0.3;
    }
  },
  mounted() {
    window.addEventListener('resize', this.calculateKeysSize);
    this.calculateKeysSize();
  },
  beforeDestroy() {
    window.removeEventListener('resize', this.calculateKeysSize);
  }
}
</script>

<template>
  <div class="play-container">
    <div class="question-checkbox-container">
      <Question class="question" v-if="!freePlay" :chords="note" :chordsname="chordName" :feedback="answer"
        :color="backgroundColor" :displayAgainBtn="tryAgainBtn" />
      <b-form-checkbox class="show-notes" v-model="checked" name="check-button" switch>
        Show notes
      </b-form-checkbox>
    </div>
    <div class="pjano-container">
      <Pjano :checked="checked" @playTone="emittedTone" />
    </div>
  </div>
  <Info v-if="!freePlay" />
</template>

<style scoped>
.play-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.question-checkbox-container {
  height: v-bind(questionCheckboxHeight + 'px');
}

.pjano-container {
  position: absolute;
  bottom: 0;
  width: 100%;
}

.question {
  display: flex;
  justify-content: center;
  width: v-bind(questionsWidth + 'px');
}

.show-notes {
  display: flex;
  justify-content: center;
  width: v-bind(showNotesWidth + 'px');
}

.check-button {
  display: flex;
  justify-content: center;
}

.showBlack {
  display: flex;
  font-size: 20px;
  color: white;
}

.form-check {
  margin: auto;
  margin-bottom: 10px;
}

@media (max-width: 1000px) {
  .pjano-container {
    position: absolute;
    bottom: 0;
    width: 100%;
  }

  .question-checkbox-container {
    display: flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    height: auto;
    position: absolute;
    top: 0;
    width: 100%;
  }
}
</style>
