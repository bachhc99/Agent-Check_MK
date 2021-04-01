# Agent-Check_MK
## 1. Cài đặt Agent trên Host giám sát (Ubuntu 20.04).
Đầu tiên, tôi sẽ giám sát host Ubuntu 20.04. Vì thế tôi sẽ tải file Deb. Trên Host Ubuntu cần giám sát, chúng ta tải agent cho nó từ server. Để lấy link tải, chúng ta vào Web UI sau đó chọn `Setup > Agents > linux` chọn file `.deb` chuột phải vào link, chọn Copy link address (Tiếng Việt: Sao chép địa chỉ liên kết)
![photo_2021-03-29_11-25-54](https://user-images.githubusercontent.com/71110126/112786971-a8140f80-9081-11eb-8b17-28dc6f0f260d.png)
![Screenshot from 2021-03-29 11-27-33](https://user-images.githubusercontent.com/71110126/112787026-c8dc6500-9081-11eb-8a12-d122762debf3.png)

## Trên server.
```
wget http://192.168.100.131/monitoring/check_mk/agents/check-mk-agent_1.2.8p21-1_all.deb
```

Cài đặt `agent` bằng lệnh

```
dpkg -i check-mk-agent-*
```

Để cho phép OMD Server được truy cập vào host, chúng ta chỉnh sửa file cấu hình `agent` trên host

```
vim /etc/xinetd.d/check_mk
```
Chỉnh sửa thông số sau
```
only_from      = 127.0.0.1 "IP của host giám sát"
```
![Screenshot from 2021-03-29 11-34-19](https://user-images.githubusercontent.com/71110126/112787538-f1189380-9082-11eb-8156-cc2285a1cb63.png)

Sau khi chỉnh xong, chúng ta lưu lại file và khởi động lại `xinetd`.

```
systemctl restart xinetd
```

Kiểm tra port đã hoạt động

```
netstat -npl | grep 6556
```
![Screenshot from 2021-03-29 11-38-47](https://user-images.githubusercontent.com/71110126/112787721-5a98a200-9083-11eb-9aae-963e37e3ebe0.png)
- **Chú ý:** Nếu sử dụng Firewall vui lòng mở port 6556. Thực hiện các bước sau:

	- Với Ubuntu:
	
	```
	ufw allow 6556/tcp
	ufw reload
	```
	
Tiếp đến, chúng ta sẽ quay lại [Web UI] để cấu hình.
### Thêm host trên Web UI
Quay trở lại Web UI, chúng ta sẽ thêm mới 1 host. Đầu tiên, Vào `Setup > Hosts > Host` và click vào `Create new host`
![Screenshot from 2021-03-29 11-42-56](https://user-images.githubusercontent.com/71110126/112788031-270a4780-9084-11eb-9829-fb7b9c00730c.png)
Điền thông tin của host của bạn như hình:
![Screenshot from 2021-03-29 11-47-23](https://user-images.githubusercontent.com/71110126/112788215-8ff1bf80-9084-11eb-8db3-9ccb65bf932b.png)
Click vào `Save & go to Services Configure`, sau đó Server sẽ thu thập thông tin từ Agent cài trên host giám sát.
Tiếp theo chọn add tất cả các plugins mà Agent đã thu thập click `Morniter undecided services` sau đó bấm `change` phía trên góc phải như hình:
![photo_2021-03-29_12-00-47](https://user-images.githubusercontent.com/71110126/112789140-997c2700-9086-11eb-95bc-bee9e6bb561a.png)
Sau đó lựa chọn `Active on selected sites` để xác nhận các thay đổi.
![photo_2021-03-29_12-00-34](https://user-images.githubusercontent.com/71110126/112798596-4eb6db00-9097-11eb-8dd8-40c45aec90f0.png)
Vậy là đã hoàn thành:
![Screenshot from 2021-03-29 11-50-35](https://user-images.githubusercontent.com/71110126/112798623-5bd3ca00-9097-11eb-98a3-45aa1c5d4033.png)

