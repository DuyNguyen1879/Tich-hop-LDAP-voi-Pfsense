# Tích hợp Pfsense với LDAP
Intergrate Pfsense with LDAP

## Yêu cầu
1. Pfsense version **pfSense-CE-2.4.4**
2. OpenLDAP
3. PHP ldapadmin

## LDAP

#### Tạo một cấu trúc như sau:

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh1.png)

#### Tạo group 

Tùy vào mục đích sử dụng sẽ tạo các group khác nhau, ở đây mình tạo group pfsense_admin

- Chọn `ou=group` -> `Create a child entry` -> `Generic: Posix Group`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh2.png)
 
- Nhập tên group là **pfsense_admin** và chọn `Create Object`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh3.png)

- Chọn commit

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh4.png)

#### Tạo user

- Chọn `ou=people` -> `Create a child entry` -> `Generic: User Account`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh5.png)

- Nhập các thông tin cần thiết sau đó chọn Creat Object

  **First name** : *huy* 
  
  **Last name**  : *tran*
  
  **Common Name**: *huytmcn*
  
  **User ID**    : *huytmuid*
  
  **Password**   :  *****
  
  **GID Number** : *pfsense_admin*
  
 ![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh6.png)

- Chọn Commit

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh7.png)

#### Mapping user với group 

> Vì mình sử dụng php ldapadmin nên một số group defaul sẽ cần phải bổ sung thêm attribute.

- Chọn `cn=pfsense_admin` -> `Add new attribute`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh8.png)

- Chọn `memberUid`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh9.png)

- Nhập user vửa tạo Sau đó chọn Update Object

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh10.png)

- Chọn Update Object

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh11.png)


> Lần sau muốn map user vào group pfsense_admin thì chỉ cần chọn (add value) và thêm user mong muốn.

Thực hiện tạo các user và group khác tùy mục đích sử dụng của bạn


## Pfsense m

Đăng nhập vào tài khoản admin 

#### Tạo mới một kiểu xác thực sử dụng LDAP 

- Chọn `System` -> `User Manager`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh12.png)

- Chọn Authentication Server

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh13.png)

- Chọn Add

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh14.png)

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
  
  ![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh15.png)
  
#### Thiết lập xác thực bằng LDAP

- Chọn Settings -> Chọn Save & Test

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh18.png)

- Kết quả 

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh19.png)
  
#### Tạo group và phân quyền

Chú ý, Group mapping trong Pfsense phải trùng tên với group trong LDAP

- Chọn Groups -> Chọn Add

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh16.png)

- Nhập thông tin như sau:
  
  **Group name** : *pfsense_admin*
  
  **Scope**      : *Remote*
  
  **Description**: *Xác thực bằng LDAP*
  
  Chọn Save
  
  ![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh17.png)
  
- Phân quyền cho Group pfsense_admin:

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh22.png)

- Chọn Add 

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh23.png)

- Chọn WebCfg- All pages -> Chọn Save -> Chọn Save tiếp 1 lần nữa 

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh24.png)

#### Kiểm tra lại cấu hình 

- Chọn `Diagnostics` -> `Authentication`

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh20.png)

- Nhập thông tin như sau 
  
  **Authentication Server** : LDAP-authen
  **Username**              : huytmcn
  **Password**              : ****** 

  Chọn Test 
  
- Kết quả 

![alt text](https://raw.githubusercontent.com/huytm/Tich-hop-LDAP-voi-Pfsense/master/images/anh21.png)

## Kiểm tra lại

Login vào pfsense với tài khoản LDAP

- huytmcn
- *******

  
                                    
