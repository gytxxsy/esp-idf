## 使用说明

**请注意： 该示例仅作为测试使用**

### 硬件需求与连线

* ESP32-S3一个
* ESP32-H2一个

接线方式如下：
ESP32S3 pin | ESP32-H2 pin
----------|-------------
   GND    |      G
   GPIO4  |      TX
   GPIO5  |      RX
   GPIO12 |      GPIO0
   GPIO13 |      GPIO1
   GPIO10 |      GPIO2
   GPIO11 |      GPIO3

编译烧录方式请参考[ot_br](README.md)以及[ot_rcp](../ot_rcp/README.md)，ot_br和ot_rcp的所有需要的配置均已默认加入各自的sdkconfig.defaults，无需额外配置。注意，默认使用三线制外部共存。

### 外部共存基础

请先阅读外部共存文档[external_coexistence_design_en.pdf](https://www.espressif.com.cn/sites/default/files/documentation/external_coexistence_design_en.pdf)，以了解共存相关的基础知识

### 测试步骤
1. 启动ot_br后，首先执行scan 功能，会得到如下现象

```
> scan

| PAN  | MAC Address      | Ch | dBm | LQI |
+------+------------------+----+-----+-----+
W(3928) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(5668) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(7428) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(9168) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(10908) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(12698) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(14448) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(16218) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(17958) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(19728) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(21508) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(23288) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(25008) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(26788) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(28528) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(30238) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
Done
> 
```
此时ESP32-S3上的外部共存还未启动，因此该现象在预期中，说明slave设备（ESP32-H2 ot_rcp）未拿到共存权限

2. 执行指令以连接wifi

```
> wifi connect -s ot_test -p espressif

ssid: ot_test
psk: espressif
I (447018) pp: pp rom version: e7ae62f
I (447018) net80211: net80211 rom version: e7ae62f
I (447028) wifi:wifi driver task: 3fcb405c, prio:23, stack:6656, core=0
I (447028) wifi:wifi firmware version: bea31f3
I (447028) wifi:wifi certification version: v7.0
I (447028) wifi:config NVS flash: enabled
I (447038) wifi:config nano formatting: disabled
I (447038) wifi:Init data frame dynamic rx buffer num: 32
I (447038) wifi:Init static rx mgmt buffer num: 5
I (447058) wifi:Init management short buffer num: 32
I (447058) wifi:Init dynamic tx buffer num: 32
I (447058) wifi:Init static tx FG buffer num: 2
I (447068) wifi:Init static rx buffer size: 1600
I (447068) wifi:Init static rx buffer num: 10
I (447068) wifi:Init dynamic rx buffer num: 32
I (447078) wifi_init: rx ba win: 6
I (447078) wifi_init: accept mbox: 6
I (447078) wifi_init: tcpip mbox: 32
I (447078) wifi_init: udp mbox: 6
I (447088) wifi_init: tcp mbox: 6
I (447088) wifi_init: tcp tx win: 5760
I (447098) wifi_init: tcp rx win: 5760
I (447098) wifi_init: tcp mss: 1440
I (447108) wifi_init: WiFi IRAM OP enabled
I (447108) wifi_init: WiFi RX IRAM OP enabled
I (447108) phy_init: phy_version 701,f4f1da3a,Mar  3 2025,15:50:10
I (447168) phy_init: Saving new calibration data due to checksum failure or outdated calibration data, mode(0)
I (447188) wifi:mode : sta (dc:da:0c:61:a4:ac)
I (447188) wifi:enable tsf
I (447188) wifi:Set ps type: 2, coexist: 0

I (447188) ot_ext_cli: Start example_connect
I (447188) example_connect: Connecting to ot_testbed_3...
W (447198) wifi:Password length matches WPA2 standards, authmode threshold changes from OPEN to WPA2
I (447208) example_connect: Waiting for IP(s)
I (449708) wifi:new:<13,2>, old:<1,0>, ap:<255,255>, sta:<13,2>, prof:1, snd_ch_cfg:0x0
I (449708) wifi:state: init -> auth (0xb0)
I (449718) wifi:state: auth -> assoc (0x0)
I (449788) wifi:state: assoc -> run (0x10)
I (449848) wifi:connected with ot_testbed_3, aid = 1, channel 13, 40D, bssid = a2:aa:3a:1c:17:c2
I (449848) wifi:security: WPA2-PSK, phy: bgn, rssi: -11
I (449848) wifi:pm start, type: 2

I (449858) wifi:dp: 1, bi: 102400, li: 3, scale listen interval from 307200 us to 307200 us
I (449858) wifi:set rx beacon pti, rx_bcn_pti: 14, bcn_timeout: 25000, mt_pti: 14, mt_time: 10000
I (449868) wifi:AP's beacon interval = 102400 us, DTIM period = 1
I (450928) esp_netif_handlers: example_netif_sta ip: 192.168.3.71, mask: 255.255.255.0, gw: 192.168.3.5
I (450928) example_connect: Got IPv4 event: Interface "example_netif_sta" address: 192.168.3.71
I (451548) example_connect: Got IPv6 event: Interface "example_netif_sta" address: fe80:0000:0000:0000:deda:0cff:fe61:a4ac, type: ESP_IP6_ADDR_IS_LINK_LOCAL
I(451568) OPENTHREAD:[N] RoutingManager: BR ULA prefix: fddc:d737:11b2::/48 (loaded)
I(451568) OPENTHREAD:[N] RoutingManager: Local on-link prefix: fdde:ad00:beef:cafe::/64
wifi sta is connected successfully
Done
I (451578) OPENTHREAD: Platform UDP bound to port 61631
I (451588) OPENTHREAD: NAT64 ready
> 
```

3. 执行command 以使能外部共存
```
> esp extcoex -e

I (521008) esp_ot_br: Enable external coex
> 
```
该指令实质上为调用两个API，如下：
* [esp_external_coex_set_work_mode](../../../components/esp_coex/src/coexist.c#L55)
* [esp_enable_extern_coex_gpio_pin](../../../components/esp_coex/src/coexist.c#L161)

随后，再次执行scan，此时应该能扫描出其他网络
```
> scan

| PAN  | MAC Address      | Ch | dBm | LQI |
+------+------------------+----+-----+-----+
| 8acc | f67eb61294e174bf | 11 | -40 |  11 |
| 026c | be780582f4d48588 | 15 | -46 |  10 |
| 1234 | 42e1b939a3bbd533 | 15 | -69 |   8 |
| aca2 | e20539f75936e5af | 19 | -45 |   8 |
| aca2 | 1ae38499d524603b | 19 | -42 |   8 |
| aca2 | 665fff42701abbf7 | 19 | -37 |   8 |
| aca2 | f6e6f6a378886728 | 19 | -53 |   9 |
| aca2 | 828f6a796186822b | 19 | -48 |  10 |
| aca2 | 2e2156f224248973 | 19 | -48 |   9 |
| aca2 | 06ab58bea210e486 | 19 | -61 |   8 |
| aca2 | 4a86cf64a7557cb5 | 19 | -41 |  11 |
| aca2 | 5a6da87cdc3d0907 | 19 | -42 |   9 |
| aca2 | 52e946d48f331bfd | 19 | -37 |   9 |
| e73b | ba30f4b0ec196699 | 20 | -40 |  11 |
| 86a6 | 4e3d2f7867097fcd | 25 | -68 |  10 |
| 1234 | 32d0870acb4b6249 | 26 | -47 |  10 |
Done
```

4. 执行command 以去使能外部共存
```
> esp extcoex -d

I (913438) esp_ot_br: Disable external coex
> 
```
该指令实质上为调用一个API，如下：
* [esp_disable_extern_coex_gpio_pin](../../../components/esp_coex/src/coexist.c#L278)

随后，再次执行scan，此时应该再次报`ChannelAccessFailure`
```
> scan

> scan

| PAN  | MAC Address      | Ch | dBm | LQI |
+------+------------------+----+-----+-----+
W(1352188) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1353878) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1355568) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1357308) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1359038) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1360778) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1362418) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1364218) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1365958) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1367678) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1369438) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1371198) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1372928) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1374708) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1376458) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
W(1378178) OPENTHREAD:[W] P-RadioSpinel-: Handle transmit done failed: ChannelAccessFailure
Done
```

5. 执行command拉高GPIO，以提供给slave 共存权限

```
> esp extcoex -g

I (1435508) gpio: GPIO[13]| InputEn: 0| OutputEn: 1| OpenDrain: 0| Pullup: 0| Pulldown: 0| Intr:0 
I (1435508) esp_ot_br: Set gpio for slave
> 

```
该指令实质上为将`grant`信号拉高，以提供给slave设备外部共存权限。具体实现可以参考代码示例[gpio_set](main/esp_ot_br.c#L247-L253)

6. 再次再次执行scan，此时应该能扫描出其他网络

```
> scan

| PAN  | MAC Address      | Ch | dBm | LQI |
+------+------------------+----+-----+-----+
| 8545 | 024c6c771e91ec11 | 11 | -71 |  10 |
| 8acc | f67eb61294e174bf | 11 | -43 |  11 |
| 026c | be780582f4d48588 | 15 | -47 |   9 |
| aca2 | 52e946d48f331bfd | 19 | -37 |   8 |
| aca2 | cace14250ea86e95 | 19 | -36 |  10 |
| aca2 | a2985a16f31614a6 | 19 | -47 |  11 |
| aca2 | 82cb006bd3446318 | 19 | -38 |  10 |
| aca2 | caf458addb73a18d | 19 | -44 |   9 |
| aca2 | 6a6f6b6ac09a7297 | 19 | -44 |  11 |
| aca2 | 2ae67d795997e0df | 19 | -59 |   9 |
| aca2 | e6f84ab354e07309 | 19 | -42 |  10 |
| aca2 | 06ab58bea210e486 | 19 | -57 |  10 |
| e73b | ba30f4b0ec196699 | 20 | -40 |  10 |
| 1234 | fe5e0541f5c12c9c | 22 | -43 |  11 |
| a481 | 7214a8644832e753 | 25 | -53 |  10 |
| 1234 | 32d0870acb4b6249 | 26 | -56 |   9 |
Done
```

至此，已经完整地实现过“开启和关闭master设备的外部共存，同时保证slave设备能在master设备外部共存关闭时能正常发包”这一需求


### 备注

* 由于四线制内部共存的tx_line为低时，slave始终能发包，因此为了保证能直观显示出外部共存对设备发包的影响，该示例采用三线制外部共存。实际使用时，如果是ESP32-S3 + ESP32-H2方案，推荐使用四线制内部共存方式
* 该示例仅编译内部共存，并保证蓝牙、内部共存、外部共存是可以同时启用的，但没有加入蓝牙的使用以及内部共存的调用。关于wifi+蓝牙的内部共存的使用与切换，请结合实际项目的需求使用。