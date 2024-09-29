---
title : "Dọn dẹp tài nguyên"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

#### Xoá môi trường CloudShell

1. Chọn **Region** chứa môi trường cần xóa
2. Trong giao diện **CloudShell**, chọn **Actions**
3. Chọn **Delete**![Clean](/images/5.cleanup/0001-cleanup.png?width=5120px)
4. Xác thực xóa môi trường bằng cách điền **Delete** và chọn **Delete**![Clean](/images/5.cleanup/0002-cleanup.png?width=5120px)

#### Xoá stack trong AWS CloudFormation console

1. Truy cập vào trang [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/)
2. Trong giao diện **CloudFormation** chọn **Stack**
3. Chọn **Stack** cần xóa
4. Chọn **Delete**
5. Xác minh xóa stack
6. Đợi vài phút stack chuyển trạng thái sang **DELETE_COMPLETE** là xóa thành công![Clean](/images/5.cleanup/0003-cleanup.png?width=5120px)

#### Xoá StackSets

1. Truy cập vào trang [AWS CloudFormation](https://console.aws.amazon.com/cloudformation/)
2. Chọn **StackSets**
3. Chọn **Stackset** cần xóa
4. Chọn **Actions** sau đó chọn **Delete StackSet**
5. Xác minh xóa stack set và chọn **Delete StackSet**

#### Xoá Lambda Functions

1. Truy cập vào trang [Lambda](https://console.aws.amazon.com/lambda/)
2. Chọn **Functions**
3. Chọn **(các) hàm** cần xoá
4. Chọn **Actions** sau đó chọn **Delete**
5. Xác minh xóa (các) hàm và chọn **Delete**

#### Xoá User

1. Truy cập vào trang [AWS IAM](https://console.aws.amazon.com/iam/)
2. Chọn **Users**
3. Chọn **User** cần xóa
4. Xác minh xóa user và chọn **Delete user**

#### Xoá Role

1. Truy cập vào trang [AWS IAM](https://console.aws.amazon.com/iam/)
2. Chọn **Roles**
3. Chọn các **Roles** cần xóa
4. Xác minh xóa role và chọn **Delete**
