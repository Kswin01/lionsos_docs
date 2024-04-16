---
weight: 30
title: "Reference system (Kitty)"
bookHidden: true
---

# libvmm API
The virtual machine library (libvmm) provides the following interfaces.

## `uintptr_t linux_setup_images(uintptr_t ram_start, uintptr_t kernel, size_t kernel_size, uintptr_t dtb_src, uintptr_t dtb_dest, size_t dtb_size, uintptr_t initrd_src, uintptr_t initrd_dest, size_t initrd_size)`

`linux_setup_images` is used to copy Linux kernel, initial RAM disc
and device tree into guest RAM ready for execution.  The first
argument *ram\_start* is the virtual address of the guest RAM.

The memory pointed to by *kernel* is checked to see that it is a Linux
image before it is copied into an appropriate place in the guest RAM.

*initrd\_dest* and *dtb\_dest* should match what is in the Device
Tree.

`linux_setup_images()` returns the kernel entry point if successful,
otherwise -1.


## `bool virq_controller_init(size_t cpu_id)`
Call to initialise the interrupt controller for a guest. This must be
done before calling `virq_register()`

## `bool virq_register(size_t vcpu_id, size_t virq_num,  virq_ack_fn_t ack_fn, void *ack_data)`

Register an interrupt with the virtual interrupt controller.
The arguments are:

  `vpcu_id` the virtual CPU number to devlvier interrupts to

  `virq_num` the IRQ number to deliver

  `ack_fn` a function to be called when the guest acknowledges the
  interrupt

    `ack_data` a cookie to be passed to the `ack_fn` when called.


## `bool virq_inject(size_t vcpu_id, int irq)`

Inject interrupt `irq` into the virtul interrupt controller on cpu `vcpu_id`

## `bool virq_register_passthrough(size_t vcpu_id, size_t irq, microkit_channel irq_ch)`

Tell the system that interrupt request `irq` is a hardware interrupt
that will be passed through to the guest.  `irq_ch` is he channel in
the System XML file that maps to that interrupt.

## `bool virq_handle_passthrough(microkit_channel irq_ch)`
Perform an interrupt injection for the interrupt registered with
`virq_register_passthrough()` for channel `irq_ch`.

## `bool guest_start(size_t boot_vcpu_id, uintptr_t kernel_pc, uintptr_t dtb, uintptr_t initrd)`
Start a guest Linux system, by passing control to the kernel entry
point.

## `void guest_stop(size_t boot_vcpu_id)`
Pause the guest.

## `bool guest_restart(size_t boot_vcpu_id, uintptr_t guest_ram_vaddr, size_t guest_ram_size)`
Restart the guest, possibly with a different image.
