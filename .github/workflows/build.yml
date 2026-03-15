#!/bin/bash

# ===========================================
# 配置 (Đã cập nhật cho Haydn 5.4 HyperOS)
# ===========================================

# 内核仓库
KERNEL_REPO="https://github.com/MiCode/Xiaomi_Kernel_OpenSource.git" # Tài hãy đổi link repo haydn của bạn tại đây
KERNEL_BRANCH="haydn-r-oss"

# KernelSU-Next (Dùng bản Next cho kernel 5.4)
ENABLE_KSU=true
KSU_SETUP_URL="https://raw.githubusercontent.com/KernelSU-Next/KernelSU-Next/next/kernel/setup.sh"
KSU_SETUP_BRANCH_ARG="next"

# SUSFS 集成 (Nhánh 5.4)
ENABLE_SUSFS=true
SUSFS_REPO="https://gitlab.com/simonpunk/susfs4ksu.git"
SUSFS_BRANCH="kernel-5.4"

# Python 版本选择 (Kernel 5.4 bắt buộc dùng Python 3)
USE_PYTHON3=true

# 工具链 (Cập nhật Clang 17 và GCC 10 thay cho bản 4.9 cũ)
CLANG_REPO="https://github.com/ZyCromerZ/Clang.git"
CLANG_BRANCH="21"
GCC64_REPO="https://github.com/AOSP-Modules/aarch64-linux-gnu-10.x.git"
GCC64_BRANCH="master"
GCC32_REPO="https://github.com/AOSP-Modules/arm-linux-gnueabi-10.x.git"
GCC32_BRANCH="master"

# AnyKernel3 打包模板
AK3_SOURCE="https://github.com/osm0sis/AnyKernel3.git"
AK3_BRANCH="master"

# 构建配置
DEFCONFIG="vendor/haydn_user_defconfig" # Kiểm tra file này trong arch/arm64/configs/
ARCH="arm64"
DEVICE="haydn"
CROSS_COMPILE_AARCH64="aarch64-linux-gnu-"
CROSS_COMPILE_ARM="arm-linux-gnueabi-"

# 构建选项
BUILD_CLEAN=false
CONCURRENT_JOBS=$(nproc)

# 输出目录
OUTPUT_DIR="$(pwd)"
RAW_ARTIFACTS_DIR="${OUTPUT_DIR}/raw_artifacts"

# ===========================================
# 脚本主体部分 (Giữ nguyên cấu trúc gốc)
# ===========================================

# 使用环境变量
KERNEL_REPO="${KERNEL_REPO_OVERRIDE:-$KERNEL_REPO}"
KERNEL_BRANCH="${KERNEL_BRANCH_OVERRIDE:-$KERNEL_BRANCH}"
ENABLE_KSU="${ENABLE_KSU_OVERRIDE:-$ENABLE_KSU}"
KSU_SETUP_URL="${KSU_SETUP_URL_OVERRIDE:-$KSU_SETUP_URL}"
KSU_SETUP_BRANCH_ARG="${KSU_SETUP_BRANCH_ARG_OVERRIDE:-$KSU_SETUP_BRANCH_ARG}"
ENABLE_SUSFS="${ENABLE_SUSFS_OVERRIDE:-$ENABLE_SUSFS}"
SUSFS_REPO="${SUSFS_REPO_OVERRIDE:-$SUSFS_REPO}"
SUSFS_BRANCH="${SUSFS_BRANCH_OVERRIDE:-$SUSFS_BRANCH}"
USE_PYTHON3="${USE_PYTHON3_OVERRIDE:-$USE_PYTHON3}"
CLANG_REPO="${CLANG_REPO_OVERRIDE:-$CLANG_REPO}"
CLANG_BRANCH="${CLANG_BRANCH_OVERRIDE:-$CLANG_BRANCH}"
GCC64_REPO="${GCC64_REPO_OVERRIDE:-$GCC64_REPO}"
GCC64_BRANCH="${GCC64_BRANCH_OVERRIDE:-$GCC64_BRANCH}"
GCC32_REPO="${GCC32_REPO_OVERRIDE:-$GCC32_REPO}"
GCC32_BRANCH="${GCC32_BRANCH_OVERRIDE:-$GCC32_BRANCH}"
AK3_SOURCE="${AK3_SOURCE_OVERRIDE:-$AK3_SOURCE}"
AK3_BRANCH="${AK3_BRANCH_OVERRIDE:-$AK3_BRANCH}"
DEFCONFIG="${DEFCONFIG_OVERRIDE:-$DEFCONFIG}"
ARCH="${ARCH_OVERRIDE:-$ARCH}"
DEVICE="${DEVICE_OVERRIDE:-$DEVICE}"
CROSS_COMPILE_AARCH64="${CROSS_COMPILE_AARCH64_OVERRIDE:-$CROSS_COMPILE_AARCH64}"
CROSS_COMPILE_ARM="${CROSS_COMPILE_ARM_OVERRIDE:-$CROSS_COMPILE_ARM}"
BUILD_CLEAN="${BUILD_CLEAN_OVERRIDE:-$BUILD_CLEAN}"
CONCURRENT_JOBS="${CONCURRENT_JOBS_OVERRIDE:-$CONCURRENT_JOBS}"
OUTPUT_DIR="${OUTPUT_DIR_OVERRIDE:-$OUTPUT_DIR}"
RAW_ARTIFACTS_DIR="${RAW_ARTIFACTS_DIR_OVERRIDE:-$RAW_ARTIFACTS_DIR}"

# 定义颜色 (Giữ nguyên)
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
PURPLE='\033[0;35m'
CYAN='\033[0;36m'
WHITE='\033[1;37m'
BRIGHT_RED='\033[1;31m'
BRIGHT_GREEN='\033[1;32m'
BRIGHT_YELLOW='\033[1;33m'
BRIGHT_BLUE='\033[1;34m'
BRIGHT_PURPLE='\033[1;35m'
BRIGHT_CYAN='\033[1;36m'
BRIGHT_WHITE='\033[1;37m'
ON_RED='\033[41m'
ON_GREEN='\033[42m'
ON_YELLOW='\033[43m'
ON_BLUE='\033[44m'
ON_MAGENTA='\033[45m'
ON_CYAN='\033[46m'
ON_WHITE='\033[47m'
BOLD='\033[1m'
UNDERLINE='\033[4m'
INVERT='\033[7m'
WHITE_ON_BLUE='\033[37;44m'
WHITE_ON_GREEN='\033[37;42m'
WHITE_ON_YELLOW='\033[37;43m'
WHITE_ON_MAGENTA='\033[37;45m'
WHITE_ON_CYAN='\033[37;46m'
NC='\033[0m' # No Color

# 显示信息 (Giữ nguyên)
echo "=============================================="
echo -e "${UNDERLINE} 构建目标 / Build Target: ${NC}"
if [ "$ENABLE_KSU" = true ] && [ "$ENABLE_SUSFS" = true ]; then
    echo -e "${BRIGHT_CYAN}🔧 构建内核 $DEVICE (带有KSU+SUSFS) ${NC}"
    echo -e "${BRIGHT_CYAN}   Build Kernel $DEVICE (with KSU+SUSFS) ${NC}"
elif [ "$ENABLE_KSU" = true ] && [ "$ENABLE_SUSFS" = false ]; then
    echo -e "${BRIGHT_CYAN}🔧 构建内核 $DEVICE (含 KernelSU) ${NC}"
    echo -e "${BRIGHT_CYAN}   Build Kernel $DEVICE (with KernelSU) ${NC}"
elif [ "$ENABLE_KSU" = false ] && [ "$ENABLE_SUSFS" = true ]; then
    echo -e "${BRIGHT_CYAN}🔧 构建内核 $DEVICE (含 SUSFS)${NC}"
    echo -e "${BRIGHT_CYAN}   Build Kernel $DEVICE (with SUSFS) ${NC}"
else
    echo -e "${BRIGHT_CYAN}🔧 构建内核 $DEVICE (不含 KernelSU/SUSFS) ${NC}"
    echo -e "${BRIGHT_CYAN}   Build Kernel  $DEVICE (without KernelSU/SUSFS) ${NC}"
fi
echo -e "${UNDERLINE} 构建配置 / Build Configuration: ${NC}"
echo -e "${BRIGHT_CYAN}🐍 Python 版本: $([ "$USE_PYTHON3" = true ] && echo "3" || echo "2")${NC}"
echo -e "${BRIGHT_CYAN}🐍 Python Version: $([ "$USE_PYTHON3" = true ] && echo "3" || echo "2")${NC}"
echo -e "${BRIGHT_CYAN}🛠️ 工具链: Clang($CLANG_BRANCH) + GCC64 + GCC32 ${NC}"
echo -e "${BRIGHT_CYAN}🛠️ Toolchains: Clang($CLANG_BRANCH) + GCC64 + GCC32 ${NC}"
echo -e "${BRIGHT_CYAN}📦 内核仓库: $KERNEL_REPO${NC}"
echo -e "${BRIGHT_CYAN}📦 Kernel Repository: $KERNEL_REPO${NC}"
echo -e "${BRIGHT_CYAN}🏷️ 内核分支: $KERNEL_BRANCH${NC}"
echo -e "${BRIGHT_CYAN}🏷️ Kernel Branch: $KERNEL_BRANCH${NC}"
echo -e "${BRIGHT_CYAN}📱 设备型号: $DEVICE${NC}"
echo -e "${BRIGHT_CYAN}📱 Device Model: $DEVICE${NC}"
echo -e "${BRIGHT_CYAN}📋 配置文件: $DEFCONFIG${NC}"
echo -e "${BRIGHT_CYAN}📋 Defconfig: $DEFCONFIG${NC}"
echo -e "${BRIGHT_CYAN}⚙️ 架构: $ARCH${NC}"
echo -e "${BRIGHT_CYAN}⚙️ Architecture: $ARCH${NC}"
echo -e "${BRIGHT_CYAN}⚡ 并发任务数: $CONCURRENT_JOBS${NC}"
echo -e "${BRIGHT_CYAN}⚡ Concurrent Jobs: $CONCURRENT_JOBS${NC}"
echo "=============================================="

# 询问用户配置是否正确
echo ""
read -p $'📋 信息是否正确? / Please Check the information ...(y/N): ' -n 1 -r CONFIRM_CONFIG
echo
if [[ ! $CONFIRM_CONFIG =~ ^[Yy]$ ]]; then
    echo -e "${WHITE_ON_RED}${BOLD}❌ 用户取消执行 / User cancelled execution ${NC}"
    exit 1
fi

# 检查是否以root权限运行
if [[ $EUID -eq 0 ]]; then
   echo -e "${WHITE_ON_RED}${BOLD}⚠️ 警告: 此脚本不应以root权限运行${NC}" 
   echo -e "${WHITE_ON_RED}${BOLD}⚠️ Warning: This script should not be run as root${NC}"
   exit 1
fi

# 更新软件包列表
echo -e "${BRIGHT_YELLOW}🔄 正在更新软件包列表... / Updating package lists...${NC}"
sudo apt-get update

# 安装依赖
echo -e "${YELLOW}📦 正在安装依赖... / Installing dependencies...${NC}"
sudo apt install -y flex zip bison cpio libssl-dev python3 python3-pip

# 配置 python
echo -e "${BRIGHT_YELLOW}🐍 正在配置 python... / Configuring python...${NC}"
if [ "$USE_PYTHON3" = true ]; then
    echo "使用 Python 3 / Using Python 3"
    sudo ln -sf /usr/bin/python3 /usr/bin/python
    python --version
else
    echo "使用 Python 2 / Using Python 2"
    sudo ln -sf /usr/bin/python2 /usr/bin/python
    python --version
fi

# 创建工作目录
WORKDIR=$(pwd)
echo -e "${BRIGHT_BLUE}📁 工作目录 / Working Directory: $WORKDIR${NC}"

# ... (Phần hỏi dọn dẹp giữ nguyên) ...
echo ""
echo -e "${BRIGHT_MAGENTA} KernelSU: $([ "$ENABLE_KSU" = true ] && echo "启用 Enabled" || echo "禁用 Disabled") | SUSFS: $([ "$ENABLE_SUSFS" = true ] && echo "启用 Enabled" || echo "禁用 Disabled") | Python: $([ "$USE_PYTHON3" = true ] && echo "3" || echo "2")${NC}"
echo -e "${BRIGHT_MAGENTA}请选择是否清理现有组件：${NC}"
read -p "是否清理之前的构建产物? / Do you want to clean previous build artifacts? (y/N): " -n 1 -r CLEAN_BUILD
echo
read -p "是否清理工具链? / Do you want to clean toolchains? (y/N): " -n 1 -r CLEAN_TOOLCHAIN
echo
read -p "是否清理内核源码? / Do you want to clean kernel source? (y/N): " -n 1 -r CLEAN_KERNEL
echo
# ... (Phần thực thi lệnh rm -rf giữ nguyên) ...
if [[ $CLEAN_BUILD =~ ^[Yy]$ ]]; then rm -rf kernel/out; fi
if [[ $CLEAN_TOOLCHAIN =~ ^[Yy]$ ]]; then rm -rf clang gcc64 gcc32; fi
if [[ $CLEAN_KERNEL =~ ^[Yy]$ ]]; then rm -rf kernel; fi

# 克隆或恢复工具链
echo -e "${BRIGHT_GREEN}📥 正在克隆或工具链... / Cloning toolchains...${NC}"
if [ ! -d "clang" ]; then git clone --depth=1 --branch $CLANG_BRANCH $CLANG_REPO clang; fi
if [ ! -d "gcc64" ]; then git clone --depth=1 --branch $GCC64_BRANCH $GCC64_REPO gcc64; fi
if [ ! -d "gcc32" ]; then git clone --depth=1 --branch $GCC32_BRANCH $GCC32_REPO gcc32; fi

# 克隆内核源码
if [ ! -d "kernel" ]; then
    echo -e "${BRIGHT_GREEN}📥 正在克隆内核源码... / Cloning kernel source...${NC}"
    git clone --depth=1 --branch $KERNEL_BRANCH $KERNEL_REPO kernel
fi

# KernelSU 集成
if [ "$ENABLE_KSU" = true ]; then
    echo -e "${BRIGHT_GREEN}🔌 正在添加 KernelSU 到内核源码树... / Adding KernelSU...${NC}"
    cd kernel
    curl -LSs "${KSU_SETUP_URL}" | bash -s $KSU_SETUP_BRANCH_ARG
    cd ..
fi

# 克隆 SUSFS
if [ "$ENABLE_SUSFS" = true ] && [ ! -d "susfs" ]; then
    git clone --depth=1 --branch $SUSFS_BRANCH $SUSFS_REPO susfs
fi

# 设置环境变量
echo -e "${BRIGHT_BLUE}⚙️  正在设置环境变量... / Setting environment variables...${NC}"
export PATH=$WORKDIR/clang/bin:$WORKDIR/gcc64/bin:$WORKDIR/gcc32/bin:$PATH

# 准备 defconfig
echo -e "${BRIGHT_BLUE}📋 正在准备 defconfig... / Preparing defconfig...${NC}"
cd kernel
make O=out ARCH=$ARCH $DEFCONFIG HOSTPYTHON=/usr/bin/python3

# 编译内核
echo ""
read -p "🎉 Tất cả đã sẵn sàng, bắt đầu biên dịch? / start building kernel? (y/N): " -n 1 -r START_BUILD
echo
if [[ ! $START_BUILD =~ ^[Yy]$ ]]; then exit 0; fi

echo -e "${BRIGHT_BLUE}🔨 正在编译内核... / Building kernel...${NC}"
# Sử dụng LLVM=1 để build kernel 5.4 hiện đại
make -j$CONCURRENT_JOBS O=out ARCH=$ARCH \
    CC=clang \
    HOSTCC=gcc \
    CLANG_TRIPLE=aarch64-linux-gnu- \
    CROSS_COMPILE=$CROSS_COMPILE_AARCH64 \
    CROSS_COMPILE_ARM32=$CROSS_COMPILE_ARM \
    LLVM=1 \
    LLVM_IAS=1 \
    HOSTPYTHON=/usr/bin/python3

# 备份并保存原始产物
echo -e "${BRIGHT_GREEN}💾 正在备份并保存原始产物... / Backing up...${NC}"
RAW_DIR="$RAW_ARTIFACTS_DIR"
mkdir -p "$RAW_DIR"
cp -v out/arch/arm64/boot/Image "$RAW_DIR/" 2>/dev/null || true

# 打包 AnyKernel3 (Giữ nguyên cấu trúc gốc)
echo -e "${BRIGHT_GREEN}📦 正在打包 AnyKernel3... / Packaging AnyKernel3...${NC}"
cd $WORKDIR
git clone --depth=1 --branch $AK3_BRANCH $AK3_SOURCE AnyKernel3
mkdir -p AnyKernel3/kernel
cp -r $RAW_ARTIFACTS_DIR/* AnyKernel3/kernel/ || true

# 修改 anykernel.sh (Cập nhật cho Haydn)
sed -i 's/^kernel.string=.*/kernel.string="Haydn Kernel 5.4 by Tài"/' AnyKernel3/anykernel.sh
sed -i 's/^device.name1=.*/device.name1=haydn/' AnyKernel3/anykernel.sh
sed -i 's/^BLOCK=.*/BLOCK=auto/' AnyKernel3/anykernel.sh

cd AnyKernel3
OUT_ZIP="$OUTPUT_DIR/${DEVICE}-kernel-5.4.zip"
zip -r "$OUT_ZIP" . -x ".git/*" "README.md"
echo -e "${BRIGHT_GREEN}🎊 Build Completed! Zip: $OUT_ZIP${NC}"

exit 0
