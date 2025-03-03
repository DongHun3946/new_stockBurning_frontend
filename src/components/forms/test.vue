


<style scoped>
/*
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
          "http://localhost:8081/api/posts/create",
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
*/
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

.post {
  display: flex;
  flex-direction: row;
  background-color: #818181;
  font-weight: 600;
  border-radius: 10px;
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

  width: 32px !important;  /* 크기 강제 지정 */
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
  color: #5ad342;
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
  flex: 1.1;
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
}

.image {
  flex: 1;
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
  width: 50%;
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