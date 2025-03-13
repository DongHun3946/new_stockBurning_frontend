<template>
  <div class="community">

    <div class="top">
      <div class="top-left">
        <span v-if="stockData">{{ stockData.stockSymbol }} 커뮤니티</span>
        <span v-else>StockBurning 커뮤니티</span>
      </div>
      <div class="top-right">
        <button class="top-button" @click="allPost">전체글</button> I
        <button class="top-button" @click="bestPost">베스트글</button>
      </div>
    </div>

    <!-- 선택한 게시글 -->
    <div class="comments-list" v-if="postAndComments">
      <div class="selected-post">
        <div class="selectedPost-left">
          <div class="selectedPost-userImage">
            <img v-if="postAndComments.postDTO.user_imagePath !== 'default_img'"
              :src="postAndComments.postDTO.user_imagePath" alt="profile_img">
            <img v-else src="/imgs/profile_32.png" alt="profile_img">
          </div>
        </div>
        <div class="selectedPost-right">
          <div class="selectedPost-top">
            <div class="selectedPost-nickname">{{ postAndComments.postDTO.nickName }}</div>
            <div class="selectedPost-createdTime"> {{ getTimeAgo(postAndComments.postDTO.createdAt) }}</div>
          </div>
          <div class="selectedPost-mid">
            <div class="selectedPost-content">{{ postAndComments.postDTO.content }}</div>
            <div class="selectedPost-image"><img :src="postAndComments.postDTO.post_imagePath" alt="게시글 이미지"></div>
          </div>
          <div class="selectedPost-bottom">
            <div class="like-button"><img src="/imgs/thumbs-up_16.png" alt="like_img"
                @click="setLike(postAndComments.postDTO)"></div>
            <div class="like">{{ postAndComments.postDTO.likes }} </div>

          </div>
        </div>
      </div>

      <!-- 댓글 -->
      <div class="comment" v-for="(comment, index) in postAndComments.commentDTOList" :key="index">
        <div class="comment-left">
          <img v-if="comment.user_imagePath !== 'default_img'" :src="comment.user_imagePath" alt="profile_img">
          <img v-else src="/imgs/profile_32.png" alt="profile_img">
        </div>
        <div class="comment-right">
          <div class="comment-top">
            <div class="comment-userNickname">{{ comment.nickName }}</div>
            <div class="comment-createdTime">{{ getTimeAgo(comment.createdAt) }}</div>
          </div>
          <div class="comment-bottom">
            <div class="comment-content">{{ comment.content }}</div>
          </div>
        </div>
      </div>

    </div>

    <div class="mid" v-if="postData && !postAndComments">
      <!-- 게시글 출력 -->
      <article v-for="(post, index) in postData" :key="index" class="post">
        <div class="profile-image">
          <img v-if="post.user_imagePath !== 'default_img'" :src="post.user_imagePath" alt="profile_img">
          <img v-else src="/imgs/profile_32.png" alt="profile_img">
        </div>
        <div class="post-content">
          <!-- 상단 -->
          <div class="nickNameAndOpinionAndTime">
            <div class="nickName">{{ post.nickName }}</div>
            <div class="opinion" :style="getOpinionStyle(post.opinion)">{{ post.opinion === 'UP' ? '상승' : post.opinion
              === 'DOWN' ? '하락' : post.opinion }}</div>
            <div class="empty-space"></div>
            <div class="createdTime">{{ getTimeAgo(post.createdAt) }} </div>
          </div>
          <!-- 중간 -->
          <div class="contentAndImage">
            <div class="content">
              {{ post.content }}
            </div>
            <div class="image" v-if="post.post_imagePath">
              <img :src="post.post_imagePath" alt="게시글 이미지">
            </div>
          </div>
          <!-- 하단 -->
          <div class="likeAndComment">
            <div class="like-button">
              <img src="/imgs/thumbs-up_16.png" alt="like_img" @click="setLike(post)">
            </div>
            <div class="like">{{ post.likes }} </div>

            <div class="comment-button">
              <img src="/imgs/comment2_16.png" alt="comment_img" @click="setComment(post.id)">
            </div>
            <div class="commentCount">
              {{ post.commentCount }}
            </div>
            <div v-if="isPostAuthor(post)" class="deleteAndModify">
              <button class="deletePost" @click="deletePost(post.id)">
                삭제
              </button>
              <button class="modifyPost" @click="modifyPost(post)">
                수정
              </button>
            </div>
            <div v-else class="deleteAndModify">
            </div>
          </div>
        </div>
      </article>
    </div>


    <div class="bottom">
      <div class="bottom-top">
        <div :contenteditable="isLoggedIn" class="custom-textarea" ref="editableDiv" @input="updateContent"
          @focus="hidePlaceholder">
          {{ placeholderText }}
        </div>
      </div>

      <div class="bottom-bottom">
        <!-- 이미지 업로드 부분 -->
        <div class="bottom-bottom-left" v-if="!postAndComments">
          <img src="/imgs/image_32.png" class="image-button" style="width: 20px; height: 20px;" alt="이미지업로드"
            @click="openFileUpload" :disabled="!isLoggedIn">
          <!-- 실제 파일 입력 요소 (숨김 처리) -->
          <input type="file" ref="fileInput" style="display: none;" @change="handleImageUpload" accept="image/*">
        </div>

        <div class="bottom-bottom-center" v-if="!postAndComments">
          <button class="up-button" @click="setUp" :disabled="!isLoggedIn" :class="{ active: up }">
            ↑상승
          </button>
          <button class="down-button" @click="setDown" :disabled="!isLoggedIn" :class="{ active: down }">
            ↓하락
          </button>
        </div>

        <div class="bottom-bottom-right">
          <button type="button" @click="cacelMode" v-show="isEditMode" class="cacel-button">
            취소
          </button>
          <button type="button"
            @click="postAndComments ? createComment() : (isEditMode ? modifyPostConfirmed() : createPost())"
            class="post-button" :disabled="!isLoggedIn">
            {{ isEditMode ? '수정' : '등록' }}
          </button>
        </div>

      </div>
    </div>
    <Modal :isVisible="isModalVisible" :option="modalOption" :titleMessage="modalTitleMessage"
      :mainMessage="modalMainMessage" :subMessage="modalSubMessage" @close="closeModal" @delete="deletePostConfirmed" />
  </div>
</template>

<script>

import apiClient from '../middleware/axios'
import { mapGetters } from 'vuex';
import Modal from './forms/ModalForm.vue';
import axios from 'axios';

export default {
  name: "StockCommunity",
  components: {
    Modal
  },
  data() {
    return {
      postContent: "",
      opinion: null,
      up: false,
      down: false,
      selectedImage: null,
      originalImage: null,

      isModalVisible: false,
      modalOption: 0,
      modalTitleMessage: "",
      modalMainMessage: "",
      modalSubMessage: "",
      isEditMode: false,

      selectedPostId: null,
      copyPostId: null,
      selectedPost: null,
      selectedComments: [],
      postAndComments: null,

      loading: false,
      isEnd: false,
      page: 0,
      size: 10,
    }
  },


  computed: { //특정 값을 계산하여 반환함
    ...mapGetters(["stockData", "postData"]),

    getNickName() {
      return this.$store.getters.nickName;
    },
    isLoggedIn() {
      return this.$store.getters.isLoggedIn;
    },
    placeholderText() {
      if (this.postAndComments) {
        // postAndComments가 있을 경우
        return this.getNickName + "님 댓글을 남겨보세요!";
      } else {
        // postAndComments가 없을 경우
        return this.isLoggedIn ? this.getNickName + "님의 의견을 남겨보세요!" : "로그인 후 의견을 남길 수 있습니다!";
      }
    },
    ticker() {
      return this.$route.query.ticker;
    },
  },
  mounted(){
    console.log("hello");
  },
  methods: {

    showModal(option, titleMessage, mainMessage, subMessage) {
      this.isModalVisible = true;
      this.modalOption = option;
      this.modalTitleMessage = titleMessage;
      this.modalMainMessage = mainMessage;
      this.modalSubMessage = subMessage;
    },
    closeModal() {
      this.isModalVisible = false;
    },

    isPostAuthor(post) {
      return this.$store.getters.id === post.user_id;
    },
    hidePlaceholder() {
      if (this.$refs.editableDiv.innerText === this.placeholderText) { //placeholderText 부분이 있을 때는 포커스 시 해당부분이 사라지도록 하고 
        this.$refs.editableDiv.innerText = "";                       //placeholderText 부분이 없을 때는 내용이 유지되도록 함
      }
    },
    /*
    async loadPosts(){
      if(this.isEnd || this.loading) return;
      this.loading = true;

      try{

      }catch(error){
        console.error("StockCommunity 게시글 출력 오류 : ", error);
      }finally{
        this.loading = false;
      }
    },
    */
    async setLike(post) {
      this.selectedPostId = post.id;
      try {
        const response = await apiClient.post(
          `http://www.stockburning.shop/api/posts/like/${this.selectedPostId}`
        );
        if (response.status === 200) {
          window.location.reload();
        }
        this.selectedPostId = null;
      } catch (error) {
        alert("다시 로그인해주세요!");
        console.error("추천 기능 오류:", error);
      }

    },
    async setComment(postId) {

      this.selectedPostId = postId;
      this.copyPostId = postId;
      try {
        const response = await axios.get(
          `http://www.stockburning.shop/api/comments/${this.selectedPostId}`
        )
        if (response.status === 200) {
          this.postAndComments = response.data;
        }
        this.selectedPostId = null;
      } catch (error) {
        console.error("StockCommunity comments error : ", error);
      }

    },
    deletePost(postId) {
      this.selectedPostId = postId;
      this.showModal(5, "주의", "삭제하시겠습니까?", "삭제를 원하지 않는 경우 취소 버튼을 눌러주세요.");
    },
    async deletePostConfirmed() {
      if (!this.selectedPostId) return;
      try {
        const response = await apiClient.delete(
          `http://www.stockburning.shop/api/posts/delete/${this.selectedPostId}`
        );
        if (response.status === 200) {
          this.isModalVisible = false;
          this.selectedPostId = null;
          window.location.reload();
        }
      } catch (error) {
        console.error("게시글 삭제 오류:", error);
        alert("(오류 발생)게시글 삭제에 실패했습니다.");
      }
    },
    cacelMode() {
      this.isEditMode = false;
      this.$refs.editableDiv.innerHTML = "";
      this.$refs.editableDiv.innerText = this.isLoggedIn ? this.getNickName + "님의 의견을 남겨보세요!" : "로그인 후 의견을 남길 수 있습니다!";
      this.selectedPostId = null;
    },
    updateContent(event) {
      this.postContent = event.target.innerHTML
        .replace(/<div>/g, '\n')  // <div> → 줄바꿈 변환
        .replace(/<\/div>/g, '')  // </div> 제거
        .replace(/<br\s*\/?>/g, '\n')  // <br> → 줄바꿈 변환
        .replace(/<img[^>]*>/g, '');  // <img> 태그 제거

      if (!event.target.innerHTML.includes('<img')) {
        this.selectedImage = null;
        if (this.isEditMode) {
          this.originalImage = 0;
        }
      }
    },
    modifyPost(post) {
      this.isEditMode = true;
      this.$refs.editableDiv.innerHTML = post.content;
      if (post.post_imagePath) {
        const imgTag = `<img src="${post.post_imagePath}" alt="게시글 이미지" style="max-width: 60px; max-height: 60px;">`;
        this.$refs.editableDiv.innerHTML += imgTag;
      }
      if(post.post_imagePath !== null){
        this.originalImage = 1;
      }
      
      this.selectedPostId = post.id;
    },

    async modifyPostConfirmed() {
      this.postContent = this.$refs.editableDiv.innerHTML
        .replace(/<div>/g, '\n')  // <div> → 줄바꿈 변환
        .replace(/<\/div>/g, '')  // </div> 제거
        .replace(/<br\s*\/?>/g, '\n')  // <br> → 줄바꿈 변환
        .replace(/<img[^>]*>/g, '');  // <img> 태그 제거;

      if (!this.postContent.trim()) {
        this.showModal(6, "정보", "내용을 입력해주세요!");
        return;
      }
      if (!this.selectedPostId) return;
      const formData = new FormData();
      const PostUpdateRequest = {
        content: this.postContent,
        opinion: this.opinion,
        originalImage : this.originalImage,
      }
      formData.append("postData", new Blob([JSON.stringify(PostUpdateRequest)],
        { type: "application/json" }));

      if (this.selectedImage) {
        formData.append("image", this.selectedImage);
      }

      try {
        const response = await apiClient.put(
          `http://www.stockburning.shop/api/posts/update/${this.selectedPostId}`,
          formData,
          {
            headers: {
              'Content-Type': 'multipart/form-data'
            }
          }
        )
        if (response.status === 200) {
          this.selectedPostId = null;
          this.selectedImage = null;
          this.postContent = "";
          this.$refs.editableDiv.innerText = "";
          window.location.reload();

        }
      } catch (error) {
        console.error("게시글 수정 실패:", error);
        alert("(오류 발생)게시글 수정에 실패했습니다.");
      }
    },
    openFileUpload() {
      if (this.isLoggedIn && this.selectedImage == null) {
        this.$refs.fileInput.click();
      }
      if (this.selectedImage !== null) {
        this.showModal(1, "정보", "이미지는 한 게시물에 하나만 게시할 수 있습니다.", "");
      }
    },
    // 이미지 업로드 처리
    handleImageUpload(event) {
      const file = event.target.files[0];
      if (file && file.type.startsWith("image/")) {
        const reader = new FileReader();
        reader.readAsDataURL(file);
        reader.onload = () => {
          this.insertImageIntoEditableDiv(reader.result);
        };
        this.selectedImage = file;
      }

    },
    insertImageIntoEditableDiv(imageSrc) {
      const imgTag = `<img src="${imageSrc}" alt="게시글 이미지" style="max-width: 60px; max-height: 60px;">`;
      this.$refs.editableDiv.innerHTML += imgTag;

    },
    // 이미지 제거
    removeImage() {
      this.selectedImage = null;
      this.$refs.fileInput.value = null;
    },

    setUp() {
      if (!this.down) {
        this.up = !this.up;
      } else { //down이 true면
        this.down = false;
        this.up = !this.up;
      }
      this.opinion = this.up ? "UP" : null;
      console.log("UP : " + this.up + ", DOWN : " + this.down + ", OPINION : " + this.opinion);
    },

    setDown() {
      if (!this.up) {
        this.down = !this.down;
      } else {
        this.up = false;
        this.down = !this.down;
      }
      this.opinion = this.down ? "DOWN" : null;
      console.log("UP : " + this.up + ", DOWN : " + this.down + ", OPINION : " + this.opinion);
    },
    allPost() {
      if (typeof this.ticker === "undefined" || this.ticker === "nasdaq") {
        this.$router.push({ path: '/stock', query: { ticker: "nasdaq", type: "allPost" } });
      }
      else {
        this.$router.push({ path: '/stock', query: { ticker: this.ticker, type: "allPost" } });
      }
    },
    bestPost() {
      if (typeof this.ticker === "undefined" || this.ticker === "nasdaq")
        this.$router.push({ path: '/stock', query: { ticker: "nasdaq", type: "bestPost" } });
      else
        this.$router.push({ path: '/stock', query: { ticker: this.ticker, type: "bestPost" } });
    },
    getOpinionStyle(opinion) {
      let textStyle = {
        fontWeight: 'bold',
        height: '18px',
        width: '15px',
        borderRadius: '30px',  // 모서리를 둥글게 설정
        padding: '5px 5px',   // 여백 추가 (배경을 더 뚜렷하게 보여주기 위함)
        display: 'flex',       // Flexbox 사용
        justifyContent: 'center', // 수평 중앙 정렬
        alignItems: 'center',  // 수직 중앙 정렬
        textAlign: 'center',   // 수평 중앙 정렬
        lineHeight: 'normal',   // 글자가 수직 중앙에 오도록 설정
      };

      if (opinion === 'UP') {
        textStyle = {
          ...textStyle,
          color: '#eb1c1c',
          backgroundColor: '#ffd2d2',  // 배경색 빨간색
          textShadow: '2px 2px 4px rgba(0, 0, 0, 0.5)', // 빨간색과 그림자
          boxShadow: '3px 3px 6px rgba(255, 0, 0, 0.3)',
        };
      } else if (opinion === 'DOWN') {
        textStyle = {
          ...textStyle,
          color: '#1a40ec',
          backgroundColor: '#d6e1fd',  // 배경색 파란색
          textShadow: '2px 2px 4px rgba(0, 0, 0, 0.5)', // 파란색과 그림자
          boxShadow: '3px 3px 6px rgba(0, 0, 255, 0.3)',
        };
      }

      return textStyle;
    },

    /*#3555d3 */
    getStockData() {
      return this.$store.getters.stockData;
    },

    async createPost() {
      if (!this.postContent.trim()) {
        this.showModal(6, "정보", "내용을 입력해주세요!");
        return;
      }
      const formData = new FormData();
      const postCreateRequest = {
        content: this.postContent,
        opinion: this.opinion,
        stockSymbol: this.getStockData() ? this.getStockData().stockSymbol : null,
      }
      formData.append("postData", new Blob([JSON.stringify(postCreateRequest)],
        { type: "application/json" }));
      if (this.selectedImage) {
        formData.append("image", this.selectedImage);
      }
      try {
        const response = await apiClient.post(
          "http://www.stockburning.shop/api/posts/create",
          formData,
          {
            headers: {
              'Content-Type': 'multipart/form-data'
            }
          }
        );
        if (response.status === 200) {
          this.postContent = "";
          this.$refs.editableDiv.innerText = "";
          window.location.reload();
        }
      } catch (error) {
        console.log(error);
      }
    },
    async createComment() {
      if (!this.postContent.trim()) {
        alert("내용을 입력해주세요!");
        return;
      }

      const commentCreateRequest = {
        content: this.postContent
      }
      try {
        const response = await apiClient.post(
          `http://www.stockburning.shop/api/comments/create/${this.copyPostId}`,
          commentCreateRequest
        );
        if (response.status === 200) {
          this.postContent = "";
          this.$refs.editableDiv.innerText = "";
          window.location.reload();
        }
      } catch (error) {
        console.error("StockCommunity createComment() error:", error);
      }
    },
    getTimeAgo(createdAt) {
      const now = new Date();
      const createdTime = new Date(createdAt);
      const diff = Math.floor((now - createdTime) / 1000); // 초 단위 차이

      if (diff < 60) {
        return `${diff}초 전`;
      } else if (diff < 3600) {
        const minutes = Math.floor(diff / 60);
        return `${minutes}분 전`;
      } else if (diff < 86400) {
        const hours = Math.floor(diff / 3600);
        return `${hours}시간 전`;
      } else {
        const days = Math.floor(diff / 86400);
        return `${days}일 전`;
      }
    },
  },
};
</script>

<style scoped>
.community {
  display: flex;
  flex-direction: column;
  height: 100vh;
  /* 화면 전체 높이 */
  color: #ffffff;
  font-size: 16px;
  background-color: #464646;
  border-radius: 30px;
  padding: 2%;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.5);
}

.top {
  flex: 0.03;
  display: flex;
  flex-direction: row;
  top: 0;
  justify-content: space-around;
  /* background-color: #9739aa; */
  align-items: center;
  color: #b4c5ee;
  padding: 10px 0;
  z-index: 10;
  text-align: center;
}

.top-left {
  /* background-color: #7fa1c5; */
  width: 65%;
  text-align: left;
  font-weight: bold;
  font-size: 19px;
  margin-left: 10px;
}

.top-right {
  /* background-color: #83d19d; */
  color: #b4c5ee;
  width: 35%;
  font-size: 14px;
  color: #bbbbbb;
}

.top-button {
  background-color: #464646;
  border: none;
  cursor: pointer;
  color: #b4c5ee;
  border-radius: 5px;
}

.top-button:hover {
  background-color: #666666;

}

.mid {
  display: flex;
  flex-direction: column;
  flex: 5;
  /* background-color: #7fa1c5; */
  margin: 10px 0;
  overflow-y: scroll;
  /* 세로 스크롤 활성화 */
  max-height: 77%;
  /* 높이 제한 */
  word-wrap: break-word;
  /* 긴 단어 줄바꿈 */
  white-space: pre-wrap;
  /* 줄바꿈 유지 */
  overflow-wrap: break-word;
  /* 컨테이너를 넘지 않도록 처리 */
  word-break: break-word;
  /* 긴 단어 강제 줄바꿈 */
}

.mid::-webkit-scrollbar {
  width: 10px;
  /* 스크롤바 너비 */
}

.mid::-webkit-scrollbar-thumb {
  background: #bbb1ab;
  /* 스크롤바 색상 */
  border-radius: 10px;
  /* 둥근 모서리 */
}

.mid::-webkit-scrollbar-thumb:hover {
  background: #92867b;
  /* 스크롤바에 마우스 올렸을 때 색상 */
  cursor: pointer;
}

.mid::-webkit-scrollbar-thumb:active {
  background: #776a60;
}

.mid::-webkit-scrollbar-track {
  background: #464646;
  /* 트랙 배경색 */
  border-radius: 10px;
}

.comments-list {
  display: flex;
  flex-direction: column;
  flex: 5;
  /* background-color: #7fa1c5; */
  margin: 10px 0;
  overflow-y: scroll;
  /* 세로 스크롤 활성화 */
  max-height: 77%;
  /* 높이 제한 */
  word-wrap: break-word;
  /* 긴 단어 줄바꿈 */
  white-space: pre-wrap;
  /* 줄바꿈 유지 */
  overflow-wrap: break-word;
  /* 컨테이너를 넘지 않도록 처리 */
  word-break: break-word;
  /* 긴 단어 강제 줄바꿈 */
}

.comments-list::-webkit-scrollbar {
  width: 10px;
  /* 스크롤바 너비 */
}

.comments-list::-webkit-scrollbar-thumb {
  background: #bbb1ab;
  /* 스크롤바 색상 */
  border-radius: 10px;
  /* 둥근 모서리 */
}

.comments-list::-webkit-scrollbar-thumb:hover {
  background: #92867b;
  /* 스크롤바에 마우스 올렸을 때 색상 */
  cursor: pointer;
}

.comments-list::-webkit-scrollbar-thumb:active {
  background: #776a60;
}

.comments-list::-webkit-scrollbar-track {
  background: #464646;
  /* 트랙 배경색 */
  border-radius: 10px;
}

.comment {
  display: flex;
  margin-left: 50px;
  flex-direction: row;
  background-color: #818181;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.5);
  font-weight: 600;
  border-radius: 10px;
  padding: 10px;
  margin-bottom: 10px;

}

.comment-left {
  flex: 1;
}

.comment-left img {
  width: 24px !important;
  /* 크기 강제 지정 */
  height: 24px !important;
  border-radius: 50%;
  object-fit: cover;
  display: block;
}

.comment-right {
  flex: 9;
  display: flex;
  flex-direction: column;
}

.comment-top {
  flex: 1;
  display: flex;
  flex-direction: row;
}

.comment-userNickname {
  flex: 3.5;
  color: #203169;
  font-weight: bold;
  /* background-color: #2fa72f; */
}

.comment-createdTime {
  color: #1f1f1f;
  flex: 1;
  /* background-color: #3555d3; */
}

.comment-bottom {
  flex: 1;

}

.comment-content {
  margin-top: 10px;
  color: #2a3236;
}

.selected-post {
  display: flex;
  flex-direction: row;
  background-color: #818181;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.5);
  font-weight: 600;
  border-radius: 10px;
  padding: 10px;
  margin-bottom: 10px;
}

.selectedPost-left {
  flex: 0.9;
  /* background-color: #2fa72f; */
}

.selectedPost-userImage img {
  width: 32px !important;
  /* 크기 강제 지정 */
  height: 32px !important;
  border-radius: 50%;
  object-fit: cover;
  display: block;
}

.selectedPost-right {
  flex: 9;
  display: flex;
  flex-direction: column;
  /* background-color: #ac2c96;*/
}

.selectedPost-top {
  flex: 1;
  display: flex;
  flex-direction: row;
  /* background-color: #b4a946;*/
}

.selectedPost-mid {
  flex: 2;
  display: flex;
  flex-direction: column;
  margin-bottom: 10px;
  /* background-color: #50c750;*/
}

.selectedPost-bottom {
  flex: 1;
  display: flex;
  flex-direction: row;
  /*background-color: #a63dc0;*/
}

.selectedPost-bottom .like-button {
  flex: 0.5;
  /*background-color: #314718;*/
}

.selectedPost-bottom .like {
  flex: 8;
  /*background-color: #31b18a;*/
  margin-right: 40px;
}

.selectedPost-nickname {
  flex: 4;
  color: #60e246;
  /*background-color: #2fa72f;*/
}

.selectedPost-createdTime {
  flex: 1;
  font-weight: bold;
  color: #1f1f1f;
  /*background-color: #a33a20;*/
}

.selectedPost-content {
  flex: 1;
  color: #2a3236;
  margin-top: 10px;
  margin-bottom: 15px;
  /*background-color: #d8721e;*/
}

.selectedPost-image {
  max-width: 200px;
  max-height: 200px;
  width: 100%;
  height: auto;
  box-sizing: border-box;
}

.selectedPost-image img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  border-radius: 5%;
}


.post {
  display: flex;
  flex-direction: row;
  background-color: #818181;
  font-weight: 600;
  border-radius: 10px;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.5);
  padding: 10px;
  margin-bottom: 10px;
}

.profile-image {
  display: flex;
  justify-content: center;
  margin-right: 10px;
  /* background-color: #dd5757; */
}

.profile-image img {

  width: 32px !important;
  /* 크기 강제 지정 */
  height: 32px !important;
  border-radius: 50%;
  object-fit: cover;
  display: block;
}

.post-content {
  /* background-color: #c9ca7b; */
  flex: 1;
  display: flex;
  flex-direction: column;
}

.nickNameAndOpinionAndTime {
  display: flex;
  flex-direction: row;
  /* background-color: #da9554; */
}

.nickName {
  flex: 1.5;
  font-weight: bold;
  color: #60e246;
  /*#5ad342 */
  /* background-color: #defa3d; */
}

.opinion {
  flex: 0.5;
  /* background-color: #2fa72f; */
}

.empty-space {
  flex: 1;
}

.createdTime {
  flex: 1;
  font-weight: bold;
  color: #1f1f1f;
  /* background-color: #94653a; */
}

.contentAndImage {
  /* background-color: #3cdf57; */
  margin-top: 10px;
  margin-bottom: 15px;
  display: flex;
  flex-direction: column;
}

.content {
  flex: 1;
  color: #2a3236;
  margin-bottom: 10px;
  /* background-color: #277fa1; */
}

.image {
  flex: 1;
  max-width: 200px;
  max-height: 200px;
  width: 100%;
  /* 부모 요소에 맞춤 */
  height: auto;
  /* 가로 비율 유지 */
  /* background-color: #d1b536; */
  box-sizing: border-box;
}

.image img {
  max-width: 100%;
  max-height: 100%;
  object-fit: contain;
  border-radius: 5%;
  /* 이미지가 넘치지 않도록 조정 */
}

.likeAndComment {
  display: flex;
  flex-direction: row;
  gap: 5px;
  /* background-color: #8e54da; */
}

.like-button {
  flex: 0.7;
}

.like-button:hover {
  cursor: pointer;
}

.like {
  flex: 1;
  color: #000000;
  /* background-color: #0c3c96; */
}

.comment-button {
  flex: 0.7;
}

.comment-button:hover {
  cursor: pointer;
}

.commentCount {
  flex: 9;
  color: #000000;
  /* background-color: #48ffff;  */
}

.deleteAndModify {
  flex: 4;
  gap: 7px;
  display: flex;
  flex-direction: row;
}

.deletePost {
  flex: 1;
  height: 100%;
  border-radius: 15px;
  background-color: #df4f4f;
  border: none;
  color: #ffffff;
  font-size: 13px;
  font-weight: bold;
  box-shadow: 0 4px 3px rgba(0, 0, 0, 0.5);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.modifyPost {
  flex: 1;
  height: 100%;
  border-radius: 15px;
  background-color: #2fa72f;
  border: none;
  color: #ffffff;
  font-size: 13px;
  font-weight: bold;
  box-shadow: 0 4px 3px rgba(0, 0, 0, 0.5);
}

.modifyPost:hover {
  cursor: pointer;
}

.deletePost:hover {
  cursor: pointer;
}

.bottom {
  flex: 1.4;
  display: flex;
  flex-direction: column;
  width: 100%;
  height: auto;
  position: sticky;
  padding: 10px;
  background-color: #686868;
  box-sizing: border-box;
  border-radius: 20px;
  /* #686868 */
}

.bottom-top {
  flex: 4;
  width: 100%;
  height: auto;
  box-sizing: border-box;
  padding: 12px;
  background-color: #5c5c5c;
  /*#5c5c5c */
  border-radius: 20px;
}

.bottom-bottom {
  flex: 1;
  display: flex;
  flex-direction: row;
  width: 100%;
  padding: 5px 0px 5px 0px;
  background-color: #686868;
  border-radius: 20px;
  /* #686868 */
  box-sizing: border-box;
  justify-content: space-between;
}

.custom-textarea {
  background-color: #5c5c5c;
  /* #5c5c5c */
  width: 100%;
  height: 100%;
  color: #2a2d2e;
  overflow-y: auto;
  max-height: 150px;
  resize: none;
  box-sizing: border-box;
  outline: none;
  padding: 0px 8px 0px 8px;
  font-size: 17px;
  font-family: 'Inter', sans-serif;
  font-weight: bold;
  border: none;

  word-wrap: break-word;
  /* 긴 단어 줄바꿈 */
  white-space: pre-wrap;
  /* 줄바꿈 유지 */
  overflow-wrap: break-word;
  /* 컨테이너를 넘지 않도록 처리 */
  word-break: break-word;
  /* 긴 단어 강제 줄바꿈 */
}


.custom-textarea::placeholder {
  color: #444444;
  font-weight: 600;
  font-family: 'Inter', sans-serif;
  font-size: 17px;
}

.custom-textarea::-webkit-scrollbar {
  width: 10px;
  overflow-y: hidden;
  background-color: #464646;
  border-radius: 10px;
}

.custom-textarea::-webkit-scrollbar-thumb {
  background: #a0a0a0;
  border-radius: 10px;
}

.custom-textarea::-webkit-scrollbar-thumb:hover {
  background: #888888;
  cursor: pointer;
}

.custom-textarea::-webkit-scrollbar-thumb:active {
  background: #1f1f1f;
}

.custom-textarea::-webkit-scrollbar-track {
  background: #464646;
  border-radius: 10px;
  height: 80%;
}

.bottom-bottom-left {
  flex: 1;
  /* background-color: #2fa72f; */
}

.bottom-bottom-center {
  flex: 2;
  /* background-color: #fac73b; */
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.bottom-bottom-right {
  flex: 1;
  /* background-color: #bb2222; */
  display: flex;
  flex-direction: row;
  gap: 10px;
}

.image-button:hover {
  cursor: pointer;
}

.up-button {
  width: 60px;
  height: 100%;
  border-radius: 15px;
  background-color: #858585;
  border: none;
  color: #c03f3f;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 3px rgba(0, 0, 0, 0.5);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.up-button:hover {
  cursor: pointer;
  transform: scale(1.1);
}

.up-button.active {
  background-color: #c03f3f;
  color: white;
}

.down-button {
  width: 60px;
  height: 100%;
  border-radius: 15px;
  background-color: #858585;
  border: none;
  color: #3555d3;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 3px rgba(0, 0, 0, 0.5);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.down-button:hover {
  cursor: pointer;
  transform: scale(1.1);
}

.down-button.active {
  background-color: #3555d3;
  color: white;
}

.post-button {
  width: 100px;
  height: 100%;
  margin-left: auto;
  border-radius: 15px;
  background-color: #458EED;
  color: white;
  border: none;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.5);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.post-button:hover {
  cursor: pointer;
  transform: scale(1.1);
}

.cacel-button {
  width: 50%;
  height: 100%;
  border-radius: 15px;
  background-color: #a8b0b9;
  color: white;
  border: none;
  font-size: 16px;
  font-weight: bold;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.5);
  transition: background-color 0.3s ease, transform 0.3s ease;
}

.cacel-button:hover {
  cursor: pointer;
  transform: scale(1.1);
}
</style>
