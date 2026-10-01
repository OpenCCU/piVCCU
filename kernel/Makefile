obj-m += eq3_char_loop.o
obj-m += plat_eq3ccu2.o
obj-m += generic_raw_uart.o
obj-m += pl011_raw_uart.o
obj-m += dw_apb_raw_uart.o
obj-m += meson_raw_uart.o
obj-m += fake_hmrf.o
obj-m += rpi_rf_mod_led.o
obj-m += rpi_rf_mod_rgb.o
obj-m += dummy_rx8130.o
obj-m += led_trigger_timer.o
obj-m += hb_rf_usb.o
obj-m += hb_rf_usb_2.o
obj-m += hb_rf_eth.o
obj-m += rtc-rx8130.o

# By default all modules are built (the DKMS package expects a module for
# every *.c file). Build systems which only need a subset of the modules
# (e.g. OpenCCU/Buildroot) can set PIVCCU_MODULES to the list of module
# names to build, e.g. PIVCCU_MODULES="generic_raw_uart hb_rf_usb".
ifneq ($(PIVCCU_MODULES),)
  obj-m := $(filter $(addsuffix .o,$(PIVCCU_MODULES)),$(obj-m))
endif

# If PIVCCU_SOC_UART_AUTO=y is set, the SoC specific raw uart drivers are
# only built if the target kernel supports the respective SoC.
ifeq ($(PIVCCU_SOC_UART_AUTO),y)
  ifneq ($(CONFIG_ARCH_BCM2835),y)
    obj-m := $(filter-out pl011_raw_uart.o,$(obj-m))
  endif
  ifneq ($(CONFIG_ARCH_ROCKCHIP),y)
    obj-m := $(filter-out dw_apb_raw_uart.o,$(obj-m))
  endif
  ifneq ($(CONFIG_ARCH_MESON),y)
    obj-m := $(filter-out meson_raw_uart.o,$(obj-m))
  endif
endif

ifneq ($(KBUILD_EXTMOD),)
# Invoked by kbuild (make -C <kernel-dir> M=<this-dir>), either via the
# 'all' target below or directly by a (cross-compiling) build system like
# Buildroot. Kbuild already provides the compiler helper macros here and
# the kernel sources are not necessarily located below /lib/modules.
ifeq ($(call cc-option-yn,-mstack-protector-guard=sysreg -mstack-protector-guard-reg=sp_el0 -mstack-protector-guard-offset=0),n)
        KBUILD_CFLAGS += -DPIVCCU_CC_HAS_NO_STACKPROTECTOR_SYSREG
endif

else

ifeq ($(KERNELRELEASE),)
  KERNELRELEASE := $(shell uname -r)
endif

ifeq ($(KERNEL_DIR),)
  KERNEL_DIR := /lib/modules/$(KERNELRELEASE)/build
endif

all:
	$(MAKE) -C $(KERNEL_DIR) M=$(PWD) modules

clean:
	$(MAKE) -C $(KERNEL_DIR) M=$(PWD) clean

endif
