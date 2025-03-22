<template>
    <div class="modify-form">
        <h1>설정</h1>
        <form @submit.prevent="submitRegistration">
            <!-- 프로필 사진 변경 -->
            <div class="form-group-1">
                <div class="form-group-1-top">
                    <label>▶ 프로필 사진</label>
                </div>
                <div class="form-group-1-mid">
                    <div class="user-image">
                        <img :src="userImageSrc" alt="User Image" />
                    </div>
                </div>
                <div class="form-group-1-bottom">
                    <div class="profile-upload">
                        <button type="button" @click="changeProfileImage" class="button-change">변경하기</button>
                        <button type="button" @click="saveProfileImage"
                            :class="['button-save', { 'button-disabled': selectedImage === null }]"
                            :disabled="selectedImage === null">저장하기</button>
                        <input type="file" ref="fileInput" style="display: none;" @change="onFileChange"
                            accept="image/*" />
                    </div>
                </div>
            </div>
            <div class="form-group-2">
                <div class="form-group-2-top">
                    <label>▶ 사용자 계정</label>
                </div>
                <div class="form-group-2-bottom">
                    <button type="button" class="button-delete" @click="deleteAccount">계정 탈퇴</button>
                </div>

            </div>



        </form>
        <Modal :isVisible="isModalVisible" :option="modalOption" :titleMessage="modalTitleMessage"
            :mainMessage="modalMainMessage" :subMessage="modalSubMessage" @close="closeModal" @delete="confirmDeleteAccount"/>

    </div>
</template>

<script>
//import axios from "axios";
import Modal from './ModalForm.vue';
import apiClient from '@/middleware/axios';
export default {
    name: "ModifySettingForm",
    components: {
        Modal,
    },
    data() {
        return {
            selectedImage: null,
            previewImage: null,
            
            isModalVisible: false,
            modalOption: "",
            modalTitleMessage: "",
            modalMainMessage: "",
            modalSubMessage: "",
        };
    },
    computed: {
        userImageSrc() {
            return this.previewImage || (this.$store.getters.userImage !== "default_img"
                ? this.$store.getters.userImage
                : "/imgs/profile_128.png");
        }
    },
    methods: {
        onFileChange(event) {
            const file = event.target.files[0];
            if (file && file.type.startsWith("image/")) {
                const reader = new FileReader();
                reader.readAsDataURL(file);
                reader.onload = () => {
                    this.previewImage = reader.result;
                    this.selectedImage = file;
                };
            }
        },
        changeProfileImage() {
            this.$refs.fileInput.click();
        },
        async saveProfileImage() {
            if (!this.selectedImage) return;
            this.$store.commit('SET_USER_IMAGE', this.previewImage);
            const formData = new FormData();
            formData.append("profileImage", this.selectedImage);

            try {
                const response = await apiClient.put("/api/modify/user/profileImage",
                    formData,
                    {
                        headers: {
                            'Content-Type': 'multipart/form-data'
                        }
                    }
                );
                if (response.status === 200) {
                    this.previewImage = null;
                    this.selectedImage = null;

                }

            } catch (error) {
                console.error("[ModifySettingForm] 프로필 사진 업로드 실패 : ", error);
            }
        },
        deleteAccount(){
            this.showModal(5, "계정 탈퇴", "탈퇴하시겠습니까?", "탈퇴를 원하지 않는 경우 취소 버튼을 눌러주세요.");
        },
        async confirmDeleteAccount() {
            try {
                console.log("탈퇴");
                const response = await apiClient.delete("/api/user/delete");
                
                if (response.status === 200) {
                    window.location.href = "/";
                    this.showModal(2, "계정 탈퇴 완료!", "계정을 성공적으로 탈퇴하였습니다!");
                }
            }
            catch (error) {
                console.error("[ModifySettingForm] 사용자 탈퇴 처리 실패 : ", error);
            }
        },
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
    },
};
</script>

<style scoped>
* {
    box-sizing: border-box;
}

.modify-form {
    width: 24%;
    min-height: 55vh;
    margin: 7% auto;
    background-color: #464646;
    padding: 20px;
    display: flex;
    flex-direction: column;
    border-radius: 15px;
    color: #fff;
    font-family: Arial, sans-serif;
    box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3);

}

.form-group-1 {
    flex: 1;
    display: flex;
    flex-direction: column;
    /* background-color: #435eb8; */
    margin-bottom: 15px;
}

.form-group-1-top {
    flex: 1;
    /* background-color: #e24313; */
}

.form-group-1-mid {
    flex: 1;
    display: flex;
    justify-content: center;
    /* 가로 가운데 정렬 */
    align-items: center;
    /* 세로 가운데 정렬 (필요시 사용) */
    /* background-color: #7bc437; */
}

.form-group-1-bottom {
    flex: 1;
    display: flex;
    justify-content: center;
    /* 가로 가운데 정렬 */
    align-items: center;
    /* 세로 가운데 정렬 (필요시 사용) */
    /* background-color: #b1912a; */
}

.form-group-2 {
    margin-top: 50px;
    flex: 1;
    display: flex;
    flex-direction: column;
    /* background-color: #e49723; */
    margin-bottom: 15px;
}

.form-group-2-top {
    flex: 1;
    /* background-color: #e24313; */
}

.form-group-2-bottom {
    flex: 1;
    /* background-color: #b1912a; */
}

.user-image img {
    width: 128px !important;
    height: 128px !important;
    border-radius: 50%;
    object-fit: cover;
    display: block;
}

h1 {
    text-align: center;
    margin-bottom: 20px;
}


label {
    display: block;
    margin-bottom: 5px;
    font-size: 18px;
    font-weight: bold;
    color: #d4caf1;
}

.profile-preview {
    /* background-color: #d35131; */
}

.profile-upload {}

.button-change {
    margin-top: 10px;
    margin-right: 5px;
}

.button-change:hover {
    background-color: #636363;
}

.button-save {
    margin-top: 10px;
    margin-left: 5px;
}

.button-save:hover {
    background-color: #636363;
}

.button-disabled {
    background-color: #3b3b3b;
    /* 비활성화 시 회색 배경 */
    cursor: not-allowed;
    /* 커서 기본으로 변경 */
}

.button-disabled:hover {
    background-color: #3b3b3b;
}

.button-delete {
    margin-top: 10px;
    width: 100%;
}

.button-delete:hover {
    background-color: #636363;
}

input {
    width: 100%;
    height: 50px;
    padding: 10px;
    font-size: 18px;
    font-weight: bold;
    color: #2b2b2b;
    border: none;
    background-color: #737373;
    border-radius: 4px;
    box-sizing: border-box;
    border-radius: 12px;
    caret-color: #3b3b3b;
    box-shadow: 0 4px 3px rgba(0, 0, 0, 0.3);
}

input:focus {
    outline: none;
}

button {
    background-color: #3b3b3b;
    color: white;
    border: none;
    padding: 5px 10px;
    border-radius: 8px;
    cursor: pointer;
    font-size: 14px;
    font-weight: bold;
    box-shadow: 0 3px 0px rgba(0, 0, 0, 0.3);
}
</style>