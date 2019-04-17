# Tích hợp Pfsense với LDAP
Intergrate Pfsense with LDAP

## Yêu cầu
1. Pfsense version **pfSense-CE-2.4.4**
2. OpenLDAP
3. PHP ldapadmin

## LDAP

#### Tạo một cấu trúc như sau:

anh 1

#### Tạo group 

Tùy vào mục đích sử dụng sẽ tạo các group khác nhau, ở đây mình tạo group pfsense_admin

- Chọn `ou=group` -> `Create a child entry` -> `Generic: Posix Group`

anh 2
 
- Nhập tên group là **pfsense_admin** và chọn `Create Object`

anh 3

- Chọn commit

anh 4

#### Tạo user

- Chọn `ou=people` -> `Create a child entry` -> `Generic: User Account`

anh 5

- Nhập các thông tin cần thiết sau đó chọn Creat Object

  **First name** : *huy* 
  **Last name**  : *tran*
  **Common Name**: *huytmcn*
  **User ID**    : *huytmuid*
  **Password**   :  *****
  **GID Number** : *pfsense_admin*
  
anh 6

- Chọn Commit

anh 7

#### Mapping user với group 

> Vì mình sử dụng php ldapadmin nên một số group defaul sẽ cần phải bổ sung thêm attribute.

- Chọn `cn=pfsense_admin` -> `Add new attribute`

anh 8

- Chọn `memberUid`

anh 9

- Nhập user vửa tạo Sau đó chọn Update Object

anh10

- Chọn Update Object

anh 11


> Lần sau muốn map user vào group pfsense_admin thì chỉ cần chọn (add value) và thêm user mong muốn.

Thực hiện tạo các user và group khác tùy mục đích sử dụng của bạn


## Pfsense m

Đăng nhập vào tài khoản admin 

#### Tạo mới một kiểu xác thực sử dụng LDAP 

- Chọn `System` -> `User Manager`

anh 12

- Chọn Authentication Server

anh 13

- Chọn Add

anh 14

- Nhập thông tin và chọn Save

  **Descriptive name**          : *LDAP-authen*
  **Type**                      : *LDAP*
  **Hostname or IP address**    : *10.10.12.193* (Địa chỉ IP LDAP server)
  **Port value**                : *389*
  **Transport**                 : *TCP - Standard*
  **Peer Certificate Authority**: *Global Root CA List*
  **Protocol version**          : *3*
  **Server Timeout**            : *25*
  **Search scope - Level**      : *Entire Subtree*
  **Search scope - BaseDN**     : *Entire Subtree*
  **Base DN**                   : *dc=nhanhoa,dc=local*
  **Authentication containers** : *ou=people,dc=nhanhoa,dc=local*
  **Extended query**            : *Uncheck*
  **Bind anonymous**            : *UnCheck*
  **Bind credentials**          : *cn=Manager,dc=nhanhoa,dc=local*  (Password *****)
  **Initial Template**          : *OpenLDAP*
  **User naming attribute**     : *cn*
  **Group naming attribute**    : *cn*
  **Group member attribute**    : *memberUid*
  **RFC 2307 Groups**           : *Check*
  **Group Object Class**        : *posixGroup*
  **UTF8 Encode**               : *Uncheck*
  **Username Alterations**      : *Uncheck*
  
  Chọn `Save`
  
  anh 15
  
#### Thiết lập xác thực bằng LDAP

- Chọn Settings -> Chọn Save & Test

anh 18

- Kết quả 

anh 19
  
#### Tạo group và phân quyền

Chú ý, Group mapping trong Pfsense phải trùng tên với group trong LDAP

- Chọn Groups -> Chọn Add

anh 16

- Nhập thông tin như sau:
  
  **Group name** : *pfsense_admin*
  **Scope**      : *Remote*
  **Description**: *Xác thực bằng LDAP*
  
  Chọn Save
  
  anh 17
  
- Phân quyền cho Group pfsense_admin:

anh 22

- Chọn Add 

anh 23

- Chọn WebCfg- All pages -> Chọn Save -> Chọn Save tiếp 1 lần nữa 

anh 24

#### Kiểm tra lại cấu hình 

- Chọn `Diagnostics` -> `Authentication`

anh 20

- Nhập thông tin như sau 
  
  **Authentication Server** : LDAP-authen
  **Username**              : huytmcn
  **Password**              : ****** 

  Chọn Test 
  
- Kết quả 

anh 21

## Kiểm tra lại

Login vào pfsense với tài khoản LDAP

- huytmcn
- *******

  
                                    
