DPDK Release 18.05
==================

.. **Read this first.**

   The text in the sections below explains how to update the release notes.

   Use proper spelling, capitalization and punctuation in all sections.

   Variable and config names should be quoted as fixed width text:
   ``LIKE_THIS``.

   Build the docs and view the output file to ensure the changes are correct::

      make doc-guides-html

      xdg-open build/doc/html/guides/rel_notes/release_18_05.html


New Features
------------

.. This section should contain new features added in this release. Sample
   format:

   * **Add a title in the past tense with a full stop.**

     Add a short 1-2 sentence description in the past tense. The description
     should be enough to allow someone scanning the release notes to
     understand the new feature.

     If the feature adds a lot of sub-features you can use a bullet list like
     this:

     * Added feature foo to do something.
     * Enhanced feature bar to do something else.

     Refer to the previous release notes for examples.

     This section is a comment. Do not overwrite or remove it.
     Also, make sure to start the actual text at the margin.
     =========================================================

* **Reworked memory subsystem.**

  Memory subsystem has been reworked to support new functionality.

  On Linux, support for reserving/unreserving hugepage memory at runtime was
  added, so applications no longer need to pre-reserve memory at startup. Due to
  reorganized internal workings of memory subsystem, any memory allocated
  through ``rte_malloc()`` or ``rte_memzone_reserve()`` is no longer guaranteed
  to be IOVA-contiguous.

  This functionality has introduced the following changes:

  * ``rte_eal_get_physmem_layout()`` was removed
  * A new flag for memzone reservation (``RTE_MEMZONE_IOVA_CONTIG``) was added
    to ensure reserved memory will be IOVA-contiguous, for use with device
    drivers and other cases requiring such memory
  * New callbacks for memory allocation/deallocation events, allowing user (or
    drivers) to be notified of new memory being allocated or deallocated
  * New callbacks for validating memory allocations above specified limit,
    allowing user to permit or deny memory allocations
  * A new command-line switch ``--legacy-mem`` to enable EAL behavior similar to
    how older versions of DPDK worked (memory segments that are IOVA-contiguous,
    but hugepages are reserved at startup only, and can never be released)
  * A new command-line switch ``--single-file-segments`` to put all memory
    segments within a segment list in a single file
  * A set of convenience function calls to look up and iterate over allocated
    memory segments
  * ``-m`` and ``--socket-mem`` command-line arguments now carry an additional
    meaning and mark pre-reserved hugepages as "unfree-able", thereby acting as
    a mechanism guaranteeing minimum availability of hugepage memory to the
    application

  Reserving/unreserving memory at runtime is not currently supported on FreeBSD.

* **Added bucket mempool driver.**

  Added bucket mempool driver which provides a way to allocate contiguous
  block of objects.
  Number of objects in the block depends on how many objects fit in
  RTE_DRIVER_MEMPOOL_BUCKET_SIZE_KB memory chunk which is build time option.
  The number may be obtained using rte_mempool_ops_get_info() API.
  Contiguous blocks may be allocated using rte_mempool_get_contig_blocks() API.

* **Added PMD-recommended Tx and Rx parameters**

  Applications can now query drivers for device-tuned values of
  ring sizes, burst sizes, and number of queues.

* **Added RSS hash and key update to CXGBE PMD.**

  Support to update RSS hash and key has been added to CXGBE PMD.

* **Added CXGBE VF PMD.**

  CXGBE VF Poll Mode Driver has been added to run DPDK over Chelsio
  T5/T6 NIC VF instances.

* **Updated mlx5 driver.**

  Updated the mlx5 driver including the following changes:

  * Introduced Multi-packet Rx. With it, achieved 100Gb/sec with 64B frames.
  * Supported to be run by non-root users given reduced set of capabilities
    CAP_NET_ADMIN, CAP_NET_RAW and CAP_IPC_LOCK.
  * Supported TSO and checksum for generic UDP and IP tunnels.
  * Supported inner checksum and RSS for GRE, VXLAN-GPE, MPLSoGRE
    and MPLSoUDP tunnels.
  * Accommodate to the new memory hotplug model.
  * Supported non virtually contiguous mempools.
  * Supported MAC adding along with allmulti and promiscuous modes from VF.
  * Supported Mellanox BlueField SoC device.
  * Supported PMD defaults for queue number and depth to improve the out
    of the box performance.

* **Updated mlx4 driver.**

  Updated the mlx4 driver including the following changes:

  * Supported to be run by non-root users given reduced set of capabilities
    CAP_NET_ADMIN, CAP_NET_RAW and CAP_IPC_LOCK.
  * Supported CRC strip toggling.
  * Accommodate to the new memory hotplug model.
  * Supported non virtually contiguous mempools.
  * Dropped support in Mellanox OFED 4.2.

* **Updated Solarflare network PMD.**

  Updated the sfc_efx driver including the following changes:

  * Added support for Solarflare XtremeScale X2xxx family adapters.
  * Added support for NVGRE, VXLAN and GENEVE filters in flow API.
  * Added support for DROP action in flow API.
  * Added support for equal stride super-buffer Rx mode (X2xxx only).
  * Added support for MARK and FLAG actions in flow API (X2xxx only).

* **Added Ethernet poll mode driver for AMD XGBE devices.**

  Added the new ``axgbe`` ethernet poll mode driver for AMD XGBE devices.
  See the :doc:`../nics/axgbe` nic driver guide for more details on this
  new driver.

* **Updated szedata2 PMD.**

  Added support for new NFB-200G2QL card.
  New API was introduced in the libsze2 library which the szedata2 PMD depends
  on thus the new version of the library was needed.
  New versions of the packages are available and the minimum required version
  is 4.4.1.

* **Added support for Broadcom NetXtreme-S (BCM58800) family of controllers (aka Stingray)**

  The BCM58800 devices feature a NetXtreme E-Series advanced network controller, a high-performance
  ARM CPU block, PCI Express (PCIe) Gen3 interfaces, key accelerators for compute offload and a high-
  speed memory subsystem including L3 cache and DDR4 interfaces, all interconnected by a coherent
  Network-on-chip (NOC) fabric.

  The ARM CPU subsystem features eight ARMv8 Cortex-A72 CPUs at 3.0 GHz, arranged in a multi-cluster
  configuration.

* **Added vDPA in vhost-user lib.**

  Added support for selective datapath in vhost-user lib. vDPA stands for vhost
  Data Path Acceleration. It supports virtio ring compatible devices to serve
  virtio driver directly to enable datapath acceleration.

* **Added IFCVF vDPA driver.**

  Added IFCVF vDPA driver to support Intel FPGA 100G VF device. IFCVF works
  as a HW vhost data path accelerator, it supports live migration and is
  compatible with virtio 0.95 and 1.0. This driver registers ifcvf vDPA driver
  to vhost lib, when virtio connected, with the help of the registered vDPA
  driver the assigned VF gets configured to Rx/Tx directly to VM's virtio
  vrings.

* **Added support for vhost dequeue interrupt mode.**

  Added support for vhost dequeue interrupt mode to release cpus to others when
  no data to transmit. Applications could register an epoll event fd to associate
  Rx queues with interrupt vectors.

* **Added support for virtio-user server mode.**
  In a container environment if the vhost-user backend restarts, there's no way
  for it to reconnect to virtio-user. To address this, support for server mode
  is added. In this mode the socket file is created by virtio-user, which the
  backend connects to. This means that if the backend restarts, it can reconnect
  to virtio-user and continue communications.

* **Added crypto workload support to vhost library.**

  New APIs are introduced in vhost library to enable virtio crypto support
  including session creation/deletion handling and translating virtio-crypto
  request into DPDK crypto operations. A sample application is also introduced.

* **Added virtio crypto PMD.**

  Added a new poll mode driver for virtio crypto devices, which provides
  AES-CBC ciphering and AES-CBC with HMAC-SHA1 algorithm-chaining. See the
  :doc:`../cryptodevs/virtio` crypto driver guide for more details on
  this new driver.

* **Added AMD CCP Crypto PMD.**

  Added the new ``ccp`` crypto driver for AMD CCP devices. See the
  :doc:`../cryptodevs/ccp` crypto driver guide for more details on
  this new driver.

* **Updated AESNI MB PMD.**

  The AESNI MB PMD has been updated with additional support for:

  * AES-CMAC (128-bit key).

* **Added Compressdev Library, a generic compression service library.**

  The compressdev library provides an API for offload of compression and
  decompression operations to hardware or software accelerator devices.

* **Added a new compression poll mode driver using Intels ISA-L.**

   Added the new ``ISA-L`` compression driver, for compression and decompression
   operations in software. See the :doc:`../compressdevs/isal` compression driver
   guide for details on this new driver.

* **Added the Event Timer Adapter Library.**

  The Event Timer Adapter Library extends the event-based model by introducing
  APIs that allow applications to arm/cancel event timers that generate
  timer expiry events. This new type of event is scheduled by an event device
  along with existing types of events.

* **Added OcteonTx TIM Driver (Event timer adapter).**

  The OcteonTx Timer block enables software to schedule events for a future
  time, it is exposed to an application via Event timer adapter library.

  See the :doc:`../eventdevs/octeontx` guide for more details

* **Added Event Crypto Adapter Library.**

    Added the Event Crypto Adapter Library.  This library extends the
    event-based model by introducing APIs that allow applications to
    enqueue/dequeue crypto operations to/from cryptodev as events scheduled
    by an event device.

* **Added Ifpga Bus, a generic Intel FPGA Bus library.**

  The Ifpga Bus library provides support for integrating any Intel FPGA device with
  the DPDK framework. It provides Intel FPGA Partial Bit Stream AFU (Accelerated
  Function Unit) scan and drivers probe.

* **Added IFPGA (Intel FPGA) Rawdev Driver.**

  Added a new Rawdev driver called IFPGA(Intel FPGA) Rawdev Driver, which cooperates
  with OPAE (Open Programmable Acceleration Engine) share code provides common FPGA
  management ops for FPGA operation.

  See the :doc:`../rawdevs/ifpga_rawdev` programmer's guide for more details.

* **Added DPAA2 QDMA Driver (in rawdev).**

  The DPAA2 QDMA is an implementation of the rawdev API, that provide means
  to initiate a DMA transaction from CPU. The initiated DMA is performed
  without CPU being involved in the actual DMA transaction.

  See the :doc:`../rawdevs/dpaa2_qdma` guide for more details.

* **Added DPAA2 Command Interface Driver (in rawdev).**

  The DPAA2 CMDIF is an implementation of the rawdev API, that provides
  communication between the GPP and NXP's QorIQ based AIOP Block (Firmware).
  Advanced IO Processor i.e. AIOP is clusters of programmable RISC engines
  optimised for flexible networking and I/O operations. The communication
  between GPP and AIOP is achieved via using DPCI devices exposed by MC for
  GPP <--> AIOP interaction.

  See the :doc:`../rawdevs/dpaa2_cmdif` guide for more details.

* **Added device event monitor framework.**

  Added a general device event monitor framework at EAL, for device dynamic management.
  Such as device hotplug awareness and actions adopted accordingly. The list of new APIs:

  * ``rte_dev_event_monitor_start`` and ``rte_dev_event_monitor_stop`` are for
    the event monitor enable and disable.
  * ``rte_dev_event_callback_register`` and ``rte_dev_event_callback_unregister``
    are for the user's callbacks register and unregister.

  Linux uevent is supported as backend of this device event notification framework.

* **Added support for procinfo and pdump on eth vdev.**

  For ethernet virtual devices (like tap, pcap, etc), with this feature, we can get
  stats/xstats on shared memory from secondary process, and also pdump packets on
  those virtual devices.

* **Added the BPF Library.**

  The BPF Library provides the ability to load and execute
  Enhanced Berkeley Packet Filter (eBPF) within user-space dpdk application.
  Also it introduces basic framework to load/unload BPF-based filters
  on eth devices (right now only via SW RX/TX callbacks).
  It also adds dependency on libelf.


API Changes
-----------

.. This section should contain API changes. Sample format:

   * Add a short 1-2 sentence description of the API change. Use fixed width
     quotes for ``rte_function_names`` or ``rte_struct_names``. Use the past
     tense.

   This section is a comment. Do not overwrite or remove it.
   Also, make sure to start the actual text at the margin.
   =========================================================

* service cores: no longer marked as experimental.

  The service cores functions are no longer marked as experimental, and have
  become part of the normal DPDK API and ABI. Any future ABI changes will be
  announced at least one release before the ABI change is made. There are no
  ABI breaking changes planned.

* eal: ``rte_lcore_has_role()`` return value changed.

  This function now returns true or false, respectively,
  rather than 0 or <0 for success or failure.
  It makes use of the function more intuitive.

* mempool: capability flags and related functions have been removed.

  Flags ``MEMPOOL_F_CAPA_PHYS_CONTIG`` and
  ``MEMPOOL_F_CAPA_BLK_ALIGNED_OBJECTS`` were used by octeontx mempool
  driver to customize generic mempool library behaviour.
  Now the new driver callbacks ``calc_mem_size`` and ``populate`` may be
  used to achieve it without specific knowledge in the generic code.

* mempool: xmem functions have been deprecated:

  - ``rte_mempool_xmem_create``
  - ``rte_mempool_xmem_size``
  - ``rte_mempool_xmem_usage``
  - ``rte_mempool_populate_iova_tab``

* mbuf: The control mbuf API has been removed in v18.05. The impacted
  functions and macros are:

  - ``rte_ctrlmbuf_init()``
  - ``rte_ctrlmbuf_alloc()``
  - ``rte_ctrlmbuf_free()``
  - ``rte_ctrlmbuf_data()``
  - ``rte_ctrlmbuf_len()``
  - ``rte_is_ctrlmbuf()``
  - ``CTRL_MBUF_FLAG``

  The packet mbuf API should be used as a replacement.

* meter: updated to accommodate configuration profiles.

  The meter API is changed to support meter configuration profiles. The
  configuration profile represents the set of configuration parameters
  for a given meter object, such as the rates and sizes for the token
  buckets. These configuration parameters were previously the part of meter
  object internal data strcuture. The separation of the configuration
  parameters from meter object data structure results in reducing its
  memory footprint which helps in better cache utilization when large number
  of meter objects are used.

* ethdev: The function ``rte_eth_dev_count``, often mis-used to iterate
  over ports, is deprecated and replaced by ``rte_eth_dev_count_avail``.
  There is also a new function ``rte_eth_dev_count_total`` to get the
  total number of allocated ports, available or not.
  The hotplug-proof applications should use ``RTE_ETH_FOREACH_DEV`` or
  ``RTE_ETH_FOREACH_DEV_OWNED_BY`` as port iterators.

* ethdev, in struct ``struct rte_eth_dev_info``, field ``rte_pci_device *pci_dev``
  replaced with field ``struct rte_device *device``.

* **Changes to semantics of rte_eth_dev_configure() parameters.**

   If both the ``nb_rx_q`` and ``nb_tx_q`` parameters are zero,
   ``rte_eth_dev_configure`` will now use PMD-recommended queue sizes, or if
   recommendations are not provided by the PMD the function will use ethdev
   fall-back values. Previously setting both of the parameters to zero would
   have resulted in ``-EINVAL`` being returned.

* **Changes to semantics of rte_eth_rx_queue_setup() parameters.**

   If the ``nb_rx_desc`` parameter is zero, ``rte_eth_rx_queue_setup`` will
   now use the PMD-recommended Rx ring size, or in the case where the PMD
   does not provide a recommendation, will use an ethdev-provided
   fall-back value. Previously, setting ``nb_rx_desc`` to zero would have
   resulted in an error.

* **Changes to semantics of rte_eth_tx_queue_setup() parameters.**

   If the ``nb_tx_desc`` parameter is zero, ``rte_eth_tx_queue_setup`` will
   now use the PMD-recommended Tx ring size, or in the case where the PMD
   does not provide a recoomendation, will use an ethdev-provided
   fall-back value. Previously, setting ``nb_tx_desc`` to zero would have
   resulted in an error.

* ethdev: several changes were made to the flow API.

  * Unused DUP action was removed.
  * Actions semantics in flow rules: list order now matters ("first
    to last" instead of "all simultaneously"), repeated actions are now
    all performed, and they do not individually have (non-)terminating
    properties anymore.
  * Flow rules are now always terminating unless a PASSTHRU action is
    present.
  * C99-style flexible arrays were replaced with standard pointers in RSS
    action and in RAW pattern item structures due to compatibility issues.
  * The RSS action was modified to not rely on external
    ``struct rte_eth_rss_conf`` anymore to instead expose its own and more
    appropriately named configuration fields directly
    (``rss_conf->rss_key`` => ``key``,
    ``rss_conf->rss_key_len`` => ``key_len``,
    ``rss_conf->rss_hf`` => ``types``,
    ``num`` => ``queue_num``), and the addition of missing RSS parameters
    (``func`` for RSS hash function to apply and ``level`` for the
    encapsulation level).
  * The VLAN pattern item (``struct rte_flow_item_vlan``) was modified to
    include inner EtherType instead of outer TPID. Its default mask was also
    modified to cover the VID part (lower 12 bits) of TCI only.
  * A new transfer attribute was added to ``struct rte_flow_attr`` in order
    to clarify the behavior of some pattern items.
  * PF and VF pattern items are now only accepted by PMDs that implement
    them (bnxt and i40e) when the transfer attribute is also present for
    consistency.
  * Pattern item PORT was renamed PHY_PORT to avoid confusion with DPDK port
    IDs.
  * An action counterpart to the PHY_PORT pattern item was added in order to
    redirect matching traffic to a specific physical port.
  * PORT_ID pattern item and actions were added to match and target DPDK
    port IDs at a higher level than PHY_PORT.

* ethdev: change flow APIs regarding count action:
  * ``rte_flow_create()`` API count action now requires the ``struct rte_flow_action_count``.
  * ``rte_flow_query()`` API parameter changed from action type to action structure.

* ethdev: changes to offload API

   A pure per-port offloading isn't requested to be repeated in [rt]x_conf->offloads to
   ``rte_eth_[rt]x_queue_setup()``. Now any offloading enabled in ``rte_eth_dev_configure()``
   can't be disabled by ``rte_eth_[rt]x_queue_setup()``. Any new added offloading which has
   not been enabled in ``rte_eth_dev_configure()`` and is requested to be enabled in
   ``rte_eth_[rt]x_queue_setup()`` must be per-queue type, otherwise trigger an error log.

* ethdev: runtime queue setup:

  ``rte_eth_rx_queue_setup`` and ``rte_eth_tx_queue_setup`` can be called after
  ``rte_eth_dev_start`` if device support runtime queue setup. Device driver can
  expose this capability through ``rte_eth_dev_info_get``. A Rx or Tx queue be
  setup at runtime need to be started explicitly by ``rte_eth_dev_rx_queue_start``
  or ``rte_eth_dev_tx_queue_start``.


ABI Changes
-----------

.. This section should contain ABI changes. Sample format:

   * Add a short 1-2 sentence description of the ABI change that was announced
     in the previous releases and made in this release. Use fixed width quotes
     for ``rte_function_names`` or ``rte_struct_names``. Use the past tense.

   This section is a comment. Do not overwrite or remove it.
   Also, make sure to start the actual text at the margin.
   =========================================================

* ring: the alignment constraints on the ring structure has been relaxed
  to one cache line instead of two, and an empty cache line padding is
  added between the producer and consumer structures. The size of the
  structure and the offset of the fields remains the same on platforms
  with 64B cache line, but change on other platforms.

* mempool: ops have changed.

  A new callback ``calc_mem_size`` has been added to ``rte_mempool_ops``
  to allow to customize required memory size calculation.
  A new callback ``populate`` has been added to ``rte_mempool_ops``
  to allow to customize objects population.
  Callback ``get_capabilities`` has been removed from ``rte_mempool_ops``
  since its features are covered by ``calc_mem_size`` and ``populate``
  callbacks.
  Callback ``register_memory_area`` has been removed from ``rte_mempool_ops``
  since the new callback ``populate`` may be used instead of it.

* **Additional fields in rte_eth_dev_info.**

  The ``rte_eth_dev_info`` structure has had two extra entries appended to the
  end of it: ``default_rxportconf`` and ``default_txportconf``. Each of these
  in turn are ``rte_eth_dev_portconf`` structures containing three fields of
  type ``uint16_t``: ``burst_size``, ``ring_size``, and ``nb_queues``. These
  are parameter values recommended for use by the PMD.

* ethdev: ABI for all flow API functions was updated.

  This includes functions ``rte_flow_copy``, ``rte_flow_create``,
  ``rte_flow_destroy``, ``rte_flow_error_set``, ``rte_flow_flush``,
  ``rte_flow_isolate``, ``rte_flow_query`` and ``rte_flow_validate``, due to
  changes in error type definitions (``enum rte_flow_error_type``), removal
  of the unused DUP action (``enum rte_flow_action_type``), modified
  behavior for flow rule actions (see API changes), removal of C99 flexible
  array from RAW pattern item (``struct rte_flow_item_raw``), complete
  rework of the RSS action definition (``struct rte_flow_action_rss``),
  sanity fix in the VLAN pattern item (``struct rte_flow_item_vlan``) and
  new transfer attribute (``struct rte_flow_attr``).

**New parameter added to rte_bbdev_op_cap_turbo_dec.**

  A new parameter ``max_llr_modulus`` has been added to
  ``rte_bbdev_op_cap_turbo_dec`` structure to specify maximal LLR (likelihood
  ratio) absolute value.

* **BBdev Queue Groups split into UL/DL Groups**

  Queue Groups have been split into UL/DL Groups in Turbo Software Driver.
  They are independent for Decode/Encode. ``rte_bbdev_driver_info`` reflects
  introduced changes.


Removed Items
-------------

.. This section should contain removed items in this release. Sample format:

   * Add a short 1-2 sentence description of the removed item in the past
     tense.

   This section is a comment. Do not overwrite or remove it.
   Also, make sure to start the actual text at the margin.
   =========================================================


Known Issues
------------

.. This section should contain new known issues in this release. Sample format:

   * **Add title in present tense with full stop.**

     Add a short 1-2 sentence description of the known issue in the present
     tense. Add information on any known workarounds.

   This section is a comment. Do not overwrite or remove it.
   Also, make sure to start the actual text at the margin.
   =========================================================

* **pdump is not compatible with old applications.**

  As we changed to use generic multi-process communication for pdump negotiation
  instead of previous dedicated unix socket way, pdump applications, including
  dpdk-pdump example and any other applications using librte_pdump, cannot work
  with older version DPDK primary applications.


Shared Library Versions
-----------------------

.. Update any library version updated in this release and prepend with a ``+``
   sign, like this:

     librte_acl.so.2
   + librte_cfgfile.so.2
     librte_cmdline.so.2

   This section is a comment. Do not overwrite or remove it.
   =========================================================


The libraries prepended with a plus sign were incremented in this version.

.. code-block:: diff

     librte_acl.so.2
     librte_bbdev.so.1
     librte_bitratestats.so.2
   + librte_bpf.so.1
     librte_bus_dpaa.so.1
     librte_bus_fslmc.so.1
     librte_bus_pci.so.1
     librte_bus_vdev.so.1
     librte_cfgfile.so.2
     librte_cmdline.so.2
   + librte_common_octeontx.so.1
   + librte_compressdev.so.1
     librte_cryptodev.so.4
     librte_distributor.so.1
   + librte_eal.so.7
   + librte_ethdev.so.9
   + librte_eventdev.so.4
     librte_flow_classify.so.1
     librte_gro.so.1
     librte_gso.so.1
     librte_hash.so.2
     librte_ip_frag.so.1
     librte_jobstats.so.1
     librte_kni.so.2
     librte_kvargs.so.1
     librte_latencystats.so.1
     librte_lpm.so.2
   + librte_mbuf.so.4
   + librte_mempool.so.4
   + librte_meter.so.2
     librte_metrics.so.1
     librte_net.so.1
     librte_pci.so.1
     librte_pdump.so.2
     librte_pipeline.so.3
     librte_pmd_bnxt.so.2
     librte_pmd_bond.so.2
     librte_pmd_i40e.so.2
     librte_pmd_ixgbe.so.2
   + librte_pmd_dpaa2_cmdif.so.1
   + librte_pmd_dpaa2_qdma.so.1
     librte_pmd_ring.so.2
     librte_pmd_softnic.so.1
     librte_pmd_vhost.so.2
     librte_port.so.3
     librte_power.so.1
     librte_rawdev.so.1
     librte_reorder.so.1
   + librte_ring.so.2
     librte_sched.so.1
     librte_security.so.1
     librte_table.so.3
     librte_timer.so.1
     librte_vhost.so.3


Tested Platforms
----------------

.. This section should contain a list of platforms that were tested with this
   release.

   The format is:

   * <vendor> platform with <vendor> <type of devices> combinations

     * List of CPU
     * List of OS
     * List of devices
     * Other relevant details...

   This section is a comment. Do not overwrite or remove it.
   Also, make sure to start the actual text at the margin.
   =========================================================

* Intel(R) platforms with Intel(R) NICs combinations

   * CPU

     * Intel(R) Atom(TM) CPU C2758 @ 2.40GHz
     * Intel(R) Xeon(R) CPU D-1541 @ 2.10GHz
     * Intel(R) Xeon(R) CPU E5-4667 v3 @ 2.00GHz
     * Intel(R) Xeon(R) CPU E5-2680 v2 @ 2.80GHz
     * Intel(R) Xeon(R) CPU E5-2699 v4 @ 2.20GHz
     * Intel(R) Xeon(R) CPU E5-2695 v4 @ 2.10GHz
     * Intel(R) Xeon(R) CPU E5-2658 v2 @ 2.40GHz
     * Intel(R) Xeon(R) CPU E5-2658 v3 @ 2.20GHz
     * Intel(R) Xeon(R) Platinum 8180 CPU @ 2.50GHz

   * OS:

     * CentOS 7.4
     * Fedora 25
     * Fedora 27
     * Fedora 28
     * FreeBSD 11.1
     * Red Hat Enterprise Linux Server release 7.3
     * SUSE Enterprise Linux 12
     * Wind River Linux 8
     * Ubuntu 14.04
     * Ubuntu 16.04
     * Ubuntu 16.10
     * Ubuntu 17.10

   * NICs:

     * Intel(R) 82599ES 10 Gigabit Ethernet Controller

       * Firmware version: 0x61bf0001
       * Device id (pf/vf): 8086:10fb / 8086:10ed
       * Driver version: 5.2.3 (ixgbe)

     * Intel(R) Corporation Ethernet Connection X552/X557-AT 10GBASE-T

       * Firmware version: 0x800003e7
       * Device id (pf/vf): 8086:15ad / 8086:15a8
       * Driver version: 4.4.6 (ixgbe)

     * Intel(R) Ethernet Converged Network Adapter X710-DA4 (4x10G)

       * Firmware version: 6.01 0x80003221
       * Device id (pf/vf): 8086:1572 / 8086:154c
       * Driver version: 2.4.6 (i40e)

     * Intel Corporation Ethernet Connection X722 for 10GbE SFP+ (4x10G)

       * Firmware version: 3.33 0x80000fd5 0.0.0
       * Device id (pf/vf): 8086:37d0 / 8086:37cd
       * Driver version: 2.4.3 (i40e)

     * Intel(R) Ethernet Converged Network Adapter XXV710-DA2 (2x25G)

       * Firmware version: 6.01 0x80003221
       * Device id (pf/vf): 8086:158b / 8086:154c
       * Driver version: 2.4.6 (i40e)

     * Intel(R) Ethernet Converged Network Adapter XL710-QDA2 (2X40G)

       * Firmware version: 6.01 0x8000321c
       * Device id (pf/vf): 8086:1583 / 8086:154c
       * Driver version: 2.4.6 (i40e)

     * Intel(R) Corporation I350 Gigabit Network Connection

       * Firmware version: 1.63, 0x80000dda
       * Device id (pf/vf): 8086:1521 / 8086:1520
       * Driver version: 5.4.0-k (igb)

* Intel(R) platforms with Mellanox(R) NICs combinations

   * CPU:

     * Intel(R) Xeon(R) Gold 6154 CPU @ 3.00GHz
     * Intel(R) Xeon(R) CPU E5-2697A v4 @ 2.60GHz
     * Intel(R) Xeon(R) CPU E5-2697 v3 @ 2.60GHz
     * Intel(R) Xeon(R) CPU E5-2680 v2 @ 2.80GHz
     * Intel(R) Xeon(R) CPU E5-2650 v4 @ 2.20GHz
     * Intel(R) Xeon(R) CPU E5-2640 @ 2.50GHz
     * Intel(R) Xeon(R) CPU E5-2620 v4 @ 2.10GHz

   * OS:

     * Red Hat Enterprise Linux Server release 7.5 (Maipo)
     * Red Hat Enterprise Linux Server release 7.4 (Maipo)
     * Red Hat Enterprise Linux Server release 7.3 (Maipo)
     * Red Hat Enterprise Linux Server release 7.2 (Maipo)
     * Ubuntu 18.04
     * Ubuntu 17.10
     * Ubuntu 16.10
     * Ubuntu 16.04
     * SUSE Linux Enterprise Server 15

   * MLNX_OFED: 4.2-1.0.0.0
   * MLNX_OFED: 4.3-2.0.2.0

   * NICs:

     * Mellanox(R) ConnectX(R)-3 Pro 40G MCX354A-FCC_Ax (2x40G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1007
       * Firmware version: 2.42.5000

     * Mellanox(R) ConnectX(R)-4 10G MCX4111A-XCAT (1x10G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 10G MCX4121A-XCAT (2x10G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 25G MCX4111A-ACAT (1x25G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 25G MCX4121A-ACAT (2x25G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 40G MCX4131A-BCAT/MCX413A-BCAT (1x40G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 40G MCX415A-BCAT (1x40G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 50G MCX4131A-GCAT/MCX413A-GCAT (1x50G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 50G MCX414A-BCAT (2x50G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 50G MCX415A-GCAT/MCX416A-BCAT/MCX416A-GCAT (2x50G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 50G MCX415A-CCAT (1x100G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 100G MCX416A-CCAT (2x100G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1013
       * Firmware version: 12.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 Lx 10G MCX4121A-XCAT (2x10G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1015
       * Firmware version: 14.21.1000 and above

     * Mellanox(R) ConnectX(R)-4 Lx 25G MCX4121A-ACAT (2x25G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1015
       * Firmware version: 14.21.1000 and above

     * Mellanox(R) ConnectX(R)-5 100G MCX556A-ECAT (2x100G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1017
       * Firmware version: 16.21.1000 and above

     * Mellanox(R) ConnectX-5 Ex EN 100G MCX516A-CDAT (2x100G)

       * Host interface: PCI Express 4.0 x16
       * Device ID: 15b3:1019
       * Firmware version: 16.21.1000 and above

* ARM platforms with Mellanox(R) NICs combinations

   * CPU:

     * Qualcomm ARM 1.1 2500MHz

   * OS:

     * Red Hat Enterprise Linux Server release 7.5 (Maipo)

   * NICs:

     * Mellanox(R) ConnectX(R)-4 Lx 25G MCX4121A-ACAT (2x25G)

       * Host interface: PCI Express 3.0 x8
       * Device ID: 15b3:1015
       * Firmware version: 14.22.0428

     * Mellanox(R) ConnectX(R)-5 100G MCX556A-ECAT (2x100G)

       * Host interface: PCI Express 3.0 x16
       * Device ID: 15b3:1017
       * Firmware version: 16.22.0428

* ARM SoC combinations from Cavium (with integrated NICs)

   * SoC:

     * Cavium CN81xx
     * Cavium CN83xx

   * OS:

     * Ubuntu 16.04.2 LTS with Cavium SDK-6.2.0-Patch2 release support package.

* ARM SoC combinations from NXP (with integrated NICs)

   * SoC:

     * NXP/Freescale QorIQ LS1046A with ARM Cortex A72
     * NXP/Freescale QorIQ LS2088A with ARM Cortex A72

   * OS:

     * Ubuntu 16.04.3 LTS with NXP QorIQ LSDK 1803 support packages
