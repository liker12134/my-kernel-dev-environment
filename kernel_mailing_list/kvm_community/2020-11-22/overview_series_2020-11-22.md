#### [PATCH kernel v2] vfio/pci/nvlink2: Do not attempt NPU2 setup on POWER8NVL NPU
##### From: Alexey Kardashevskiy <aik@ozlabs.ru>

```c
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Alexey Kardashevskiy <aik@ozlabs.ru>
X-Patchwork-Id: 11923797
Return-Path: <kvm-owner@kernel.org>
X-Spam-Checker-Version: SpamAssassin 3.4.0 (2014-02-07) on
	aws-us-west-2-korg-lkml-1.web.codeaurora.org
X-Spam-Level: 
X-Spam-Status: No, score=-15.1 required=3.0 tests=BAYES_00,
	HEADER_FROM_DIFFERENT_DOMAINS,INCLUDES_CR_TRAILER,INCLUDES_PATCH,
	MAILING_LIST_MULTI,SPF_HELO_NONE,SPF_PASS,URIBL_BLACK,USER_AGENT_GIT
	autolearn=ham autolearn_force=no version=3.4.0
Received: from mail.kernel.org (mail.kernel.org [198.145.29.99])
	by smtp.lore.kernel.org (Postfix) with ESMTP id D3A1CC5519F
	for <kvm@archiver.kernel.org>; Sun, 22 Nov 2020 07:40:04 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 88BF92078E
	for <kvm@archiver.kernel.org>; Sun, 22 Nov 2020 07:40:04 +0000 (UTC)
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1727315AbgKVHkD (ORCPT <rfc822;kvm@archiver.kernel.org>);
        Sun, 22 Nov 2020 02:40:03 -0500
Received: from ozlabs.ru ([107.174.27.60]:60510 "EHLO ozlabs.ru"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S1727286AbgKVHkD (ORCPT <rfc822;kvm@vger.kernel.org>);
        Sun, 22 Nov 2020 02:40:03 -0500
Received: from fstn1-p1.ozlabs.ibm.com (localhost [IPv6:::1])
        by ozlabs.ru (Postfix) with ESMTP id A6F9AAE80249;
        Sun, 22 Nov 2020 02:39:57 -0500 (EST)
From: Alexey Kardashevskiy <aik@ozlabs.ru>
To: linuxppc-dev@lists.ozlabs.org
Cc: Alexey Kardashevskiy <aik@ozlabs.ru>,
 David Gibson <david@gibson.dropbear.id.au>, kvm-ppc@vger.kernel.org,
	=?utf-8?q?Leonardo_Augusto_Guimar=C3=A3es_Garcia?=  <lagarcia@br.ibm.com>,
 Michael Ellerman <mpe@ellerman.id.au>, kvm@vger.kernel.org,
 Alex Williamson <alex.williamson@redhat.com>, stable@vger.kernel.org
Subject: [PATCH kernel v2] vfio/pci/nvlink2: Do not attempt NPU2 setup on
 POWER8NVL NPU
Date: Sun, 22 Nov 2020 18:39:50 +1100
Message-Id: <20201122073950.15684-1-aik@ozlabs.ru>
X-Mailer: git-send-email 2.17.1
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

We execute certain NPU2 setup code (such as mapping an LPID to a device
in NPU2) unconditionally if an Nvlink bridge is detected. However this
cannot succeed on POWER8NVL machines as the init helpers return an error
other than ENODEV which means the device is there is and setup failed so
vfio_pci_enable() fails and pass through is not possible.

This changes the two NPU2 related init helpers to return -ENODEV if
there is no "memory-region" device tree property as this is
the distinction between NPU and NPU2.

Tested on
- POWER9 pvr=004e1201, Ubuntu 19.04 host, Ubuntu 18.04 vm,
  NVIDIA GV100 10de:1db1 driver 418.39
- POWER8 pvr=004c0100, RHEL 7.6 host, Ubuntu 16.10 vm,
  NVIDIA P100 10de:15f9 driver 396.47

Fixes: 7f92891778df ("vfio_pci: Add NVIDIA GV100GL [Tesla V100 SXM2] subdriver")
Cc: stable@vger.kernel.org # 5.0
Signed-off-by: Alexey Kardashevskiy <aik@ozlabs.ru>
---
Changes:
v2:
* updated commit log with tested configs and replaced P8+ with POWER8NVL for clarity
---
 drivers/vfio/pci/vfio_pci_nvlink2.c | 7 +++++--
 1 file changed, 5 insertions(+), 2 deletions(-)

```
