1.ILedService.aidl�ļ�����D:\android_projects\android_system_code\frameworks\base\core\java\android\os
2.�޸�frameworks/base/Android.mk ���һ��
					core/java/android/os/IVibratorService.aidl
					core/java/android/os/ILedService.aidl
3. mmm frameworks/base/
4.���� .out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/src/core/java/android/os/ILedService.java

��2�� Server : LedService.java
								SystemServer.java
							
								
			�����ļ��ϴ���������
				frameworks/base/services/java/com/android/server/SystemServer.java
				frameworks/base/services/core/java/com/android/server/LedService.java
				
				
	����Ҫ�޸�frameworks/base/services/core/Android.mk
		LOCAL_SRC_FILES += \ 
			${call all-java-files-under, java}
			
(3) JNI :
			com_android_server_LedService.cpp
			onload.cpp
		
		frameworks/base/services/core/jni/onload.cpp
		frameworks/base/services/core/jni/com_android_server_LedService
		
	�޸�frameworks/base/services/core/jni/Android.mk
	 $(LOCAL_REL_DIR)/com_android_server_VibratorService.cpp \
   + $(LOCAL_REL_DIR)/com_android_server_LedService.cpp \
   
 ����
 $ mmm frameworks/base/services/
 $ make snod
 $ ./gen-img.sh
 
 out/target/product/tiny4412/system.img
 
 
 ��дapp
 ILedService iLedService = ILedService.Stub.asInterface(ServiceManager.getService("led"));
	a.����ʲô
		����out/target/common/obj/JAVA_LIBRARIES/framework_intermediates/classess.jar
	b.��ô���� ��google ����android studio jar��
	
	�޸�project/app/src/build.gradle
    dexOptions {
        javaMaxHeapSize "4g"
    }
    
     defaultConfig {
     //���Enabling  multidex support.
     multiDexEnabled true 
     }
     
     dependencies {
     	compile 'com.android.support:multidex:1.0.0'
     }
   �޸� mainfest
    <application
    //���
    android:name="android.support.multidex.MultiDexApplication"
    
    
  V3	
  (3)	JNI:�����ϴ�
  frameworks/base/services/core/jni/com_android_server_LedService.cpp
  (4)HAL:hal_led.c hal_led.h
  hardware/libhardware/include/hardware/led_hal.h
  hardware/libhardware/modules/led/led_hal.c
  hardware/libhardware/modules/led/Android.n k
  
  Android.mk:
  LOCAL_PATH := $(call my-dir)
  include $(CLEAR_VARS)
  LOCAL_MODULE :=led.default
  LOCAL_MODULE_RELATIVE_PATH := hw
  LOCAL_C_INCLUDES := hardware/libhardware
  LOCAL_SRC_FILES := led_hal.c
  LOCAL_SHARED_LIBRARYS := liblog
  LOCAL_MODULE_TAGS := eng
  
  include $(BUILD_SHARED_LIBRARY)
   
  ����
  $  mmm frameworks/base/services/
	$ mmm hardware/libhardware/modules/led
	$ make snod
	$./gen-img.sh
	
	logcat LedHal:I *:s
	
HAL:
	lights.c //lights.h
	//hardware/libhardware/include/hardware/lights.h
	hardware/libhardware/modules/lights/lights.c
	hardware/libhardware/modules/lights/Android.mk
	
	  Android.mk:
  LOCAL_PATH := $(call my-dir)
  include $(CLEAR_VARS)
  LOCAL_MODULE :=lights.tiny4412
  LOCAL_MODULE_RELATIVE_PATH := hw
  LOCAL_C_INCLUDES := hardware/libhardware
  LOCAL_SRC_FILES := lights.c
  LOCAL_SHARED_LIBRARYS := liblog
  LOCAL_MODULE_TAGS := eng
  
  include $(BUILD_SHARED_LIBRARY)
  
    ����
    	$ �޸� vi vendor/friendly-arm/tiny4412/device-tiny4412.mk
		#PRODUCT_COPY_FILES += \
        $(VENDOR_PATH)/proprietary/lights.tiny4412.so:system/lib/hw/lights.tiny4412.so
	$ mmm hardware/libhardware/modules/lights
	$ make snod
	$./gen-img.sh
	
	diff vendor/friendly-arm/tiny4412/proprietary/lights.tiny4412.so  out/target/product/tiny4412/system/lib/hw/lights.tiny4412.so
	
	�ں�Ҳ��Ҫ�޸�
	1.drivers/leds/led-class.c : 0644��Ϊ0666
					static struct device_attribute led_class_attrs[] = {
				__ATTR(brightness, 0666, led_brightness_show, led_brightness_store),
				__ATTR(max_brightness, 0444, led_max_brightness_show, NULL),
			#ifdef CONFIG_LEDS_TRIGGERS
				__ATTR(trigger, 0666, led_trigger_show, led_trigger_store),
			#endif
				__ATTR_NULL,
			};
	2.drivers/leds/ledtrig-timer.c : 0644��Ϊ0666
			#if defined(CONFIG_MACH_IPCAM)
		static DEVICE_ATTR(delay_on, 0666, led_delay_on_show, led_delay_on_store);
		static DEVICE_ATTR(delay_off, 0666, led_delay_off_show, led_delay_off_store);
		#else
		static DEVICE_ATTR(delay_on, 0666, led_delay_on_show, led_delay_on_store);
		static DEVICE_ATTR(delay_off, 0666, led_delay_off_show, led_delay_off_store);
		#endif
	3.make zImage