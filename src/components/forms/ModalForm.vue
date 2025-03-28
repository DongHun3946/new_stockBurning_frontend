<template>
  <transition name="fade">
    <div v-if="isVisible" class="modal-overlay">
      <div class="modal-content">
        <p class="modal-content-title" :style="titleStyle">{{ titleMessage }}</p>
        <p class="modal-content-main">{{ mainMessage }}</p>
        <p class="modal-content-sub">{{ subMessage }}</p>
        <p v-if="option === 4" class="progress-text">{{ progressMessage }}</p>
        <div class="buttons">
          <button v-show="option !== 4 && option !== 6" @click="handleButtonClick">확인</button>
          <button v-show="option === 5" @click="handleButtonCancel">취소</button>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  /* Option 1 : 실패 (타이틀 : 빨강색) 확인버튼 누르면 모달창만 닫힘
            2 : 성공 (타이틀 : 초록색) 확인버튼 누르면 모달창 닫히고 홈으로 이동 및 로그아웃됨
            3 : 실패 (타이틀 : 빨강색) 확인버튼 누르면 모달창 닫히고 홈으로 이동
            4 : 애니메이션 효과부여
            5 : 삭제버튼(타이틀 : 빨강색) 확인버튼 누르면 삭제, 취소버튼 누르면 모달창만 닫힘
            6 : 게시글 입력칸에 내용없이 등록버튼 눌렀을 시 나타나는 모달창
  */
  data() {
    return {
      progressMessage: "",
      intervalId: null,
    }
  },
  props: {
    isVisible: { type: Boolean, required: true },
    option: { type: Number, required: false },
    titleMessage: { type: String, default: '' },
    mainMessage: { type: String, default: '' },
    subMessage: { type: String, default: '' },
    inputProgressMessage: { type: String, default: '' },
  },

  methods: {
    closeModal() {
      this.$emit('close');
    },
    completeModal() {
      this.$emit('close');
      this.$router.push("/");
    },
    handleButtonClick() {
      if (this.option === 1) {
        this.closeModal();
      } else if (this.option === 2) {
        this.completeModal();
        this.$store.dispatch('logout');
      } else if (this.option === 3) {
        this.completeModal();
      } else if (this.option === 5) {
        this.$emit('delete');
      }
    },
    handleButtonCancel() {
      this.closeModal();
    },
    startAnimation() {
      if (this.intervalId) return;
      let count = 0;
      this.intervalId = setInterval(() => {
        count = (count + 1) % 4;
        this.progressMessage = this.inputProgressMessage + ".".repeat(count);
      }, 500); //0.5초마다 애니메이션 효과
    },
    stopAnimation() {
      if (this.intervalId) {
        clearInterval(this.intervalId);
        this.intervalId = null;
      }
    },
    autoClose() {
      setTimeout(() => {
        this.closeModal();
      }, 1000); // 3초 후 자동 닫힘
    },
  },
  watch: {
    option(newValue) {
      if (newValue === 4) {
        this.startAnimation();
      } else if (newValue === 6) {
        this.autoClose();
      }
    },
    isVisible(newValue) {
      if (newValue && this.option === 6) {
        this.autoClose();
      }
    }
  },
  computed: {
    titleStyle() {
      return this.option === 2 ? { color: '#64da6a' } : { color: '#e24040' };
    }
  },

};
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 50%;
  /* 화면의 세로 중간 */
  left: 50%;
  /* 화면의 가로 중간 */
  transform: translate(-50%, -50%);
  /* 정확히 중앙 정렬 */
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-content {
  background: rgb(51, 51, 51);
  padding: 5px 30px 15px 30px;
  border-radius: 10px;
  text-align: center;
  font-size: bold;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3),
    0 12px 24px rgba(0, 0, 0, 0.15);
}

.modal-content-title {
  font-size: 30px;
  font-weight: bold;
}

.modal-content-main {
  font-size: 20px;
  font-weight: bold;
}

.modal-content-sub {
  font-size: 15px;
}

.progress-text {
  font-size: 20px;
  font-weight: bold;
}

button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #636363;
  color: white;
  font-weight: bold;
  font-size: 15px;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

button:hover {
  background-color: #a3a3a3;
}

.buttons {
  display: flex;
  flex-direction: row;
  gap: 10px;
  justify-content: center;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 1s ease;
}

.fade-enter,
.fade-leave-to {
  opacity: 0;
}
</style>