

#### [PATCH 0/3] KVM: arm64: Another set of CSV2-related fixes
##### From: Marc Zyngier <maz@kernel.org>
X-SA-Exim-Mail-From: maz@kernel.org

```c
From patchwork Tue Nov  3 17:14:42 2020
Content-Type: text/plain; charset="utf-8"
MIME-Version: 1.0
Content-Transfer-Encoding: 7bit
X-Patchwork-Submitter: Marc Zyngier <maz@kernel.org>
X-Patchwork-Id: 11878435
Return-Path: <SRS0=IfBm=EJ=vger.kernel.org=kvm-owner@kernel.org>
X-Spam-Checker-Version: SpamAssassin 3.4.0 (2014-02-07) on
	aws-us-west-2-korg-lkml-1.web.codeaurora.org
X-Spam-Level: 
X-Spam-Status: No, score=-7.1 required=3.0 tests=BAYES_00,DKIMWL_WL_HIGH,
	DKIM_SIGNED,DKIM_VALID,DKIM_VALID_AU,MAILING_LIST_MULTI,SPF_HELO_NONE,
	SPF_PASS,USER_AGENT_GIT autolearn=no autolearn_force=no version=3.4.0
Received: from mail.kernel.org (mail.kernel.org [198.145.29.99])
	by smtp.lore.kernel.org (Postfix) with ESMTP id C50C9C2D0A3
	for <kvm@archiver.kernel.org>; Tue,  3 Nov 2020 17:14:54 +0000 (UTC)
Received: from vger.kernel.org (vger.kernel.org [23.128.96.18])
	by mail.kernel.org (Postfix) with ESMTP id 76670216C4
	for <kvm@archiver.kernel.org>; Tue,  3 Nov 2020 17:14:54 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
	s=default; t=1604423694;
	bh=6+qWLb2RuYMP6Lh4W5V8A6bsv6YMBX9ny8ju+VAAdW0=;
	h=From:To:Cc:Subject:Date:List-ID:From;
	b=sCr1clubJKaJrGekqOZX2Sc3f18g2QoNNgRZgCPIZ0LQqDeOGCp58xri+ZZ1VChSK
	 XHpvQTDG6ZCY+8A+eLKhxkgHdfb7VgIVbM+RAafmAgAqjzsSsWCSkO27N/zq4f/Aoi
	 WtKdA5+boYQLLYydCPfh20ncpWLxjEFsL3A0Ok5U=
Received: (majordomo@vger.kernel.org) by vger.kernel.org via listexpand
        id S1728415AbgKCROx (ORCPT <rfc822;kvm@archiver.kernel.org>);
        Tue, 3 Nov 2020 12:14:53 -0500
Received: from mail.kernel.org ([198.145.29.99]:40612 "EHLO mail.kernel.org"
        rhost-flags-OK-OK-OK-OK) by vger.kernel.org with ESMTP
        id S1727901AbgKCROw (ORCPT <rfc822;kvm@vger.kernel.org>);
        Tue, 3 Nov 2020 12:14:52 -0500
Received: from disco-boy.misterjones.org (disco-boy.misterjones.org
 [51.254.78.96])
        (using TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits))
        (No client certificate requested)
        by mail.kernel.org (Postfix) with ESMTPSA id 4FA8C20E65;
        Tue,  3 Nov 2020 17:14:52 +0000 (UTC)
DKIM-Signature: v=1; a=rsa-sha256; c=relaxed/simple; d=kernel.org;
        s=default; t=1604423692;
        bh=6+qWLb2RuYMP6Lh4W5V8A6bsv6YMBX9ny8ju+VAAdW0=;
        h=From:To:Cc:Subject:Date:From;
        b=c6PdVPRRjCGBWTLYuOFAf1qeWgIZ2ltAw/wGAgbd4g0BbkiOImKrkusWHZw8fFk7a
         4LJ9vA+G/dU29gHYkLy0oL/KLttVC9huKkE5mBtOFfpajFY92HGKCnvbRZm2kipzjq
         vFzRxMgMlwEQJA3PWPB22JZBPxaiACJFr1gFv7AA=
Received: from 78.163-31-62.static.virginmediabusiness.co.uk ([62.31.163.78]
 helo=why.lan)
        by disco-boy.misterjones.org with esmtpsa  (TLS1.3) tls
 TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        (Exim 4.94)
        (envelope-from <maz@kernel.org>)
        id 1kZztS-007CyW-4V; Tue, 03 Nov 2020 17:14:50 +0000
From: Marc Zyngier <maz@kernel.org>
To: kvm@vger.kernel.org, kvmarm@lists.cs.columbia.edu,
        linux-arm-kernel@lists.infradead.org
Cc: Peng Liang <liangpeng10@huawei.com>, Will Deacon <will@kernel.org>,
        James Morse <james.morse@arm.com>,
        Julien Thierry <julien.thierry.kdev@gmail.com>,
        Suzuki K Poulose <suzuki.poulose@arm.com>,
        kernel-team@android.com
Subject: [PATCH 0/3] KVM: arm64: Another set of CSV2-related fixes
Date: Tue,  3 Nov 2020 17:14:42 +0000
Message-Id: <20201103171445.271195-1-maz@kernel.org>
X-Mailer: git-send-email 2.28.0
MIME-Version: 1.0
X-SA-Exim-Connect-IP: 62.31.163.78
X-SA-Exim-Rcpt-To: kvm@vger.kernel.org, kvmarm@lists.cs.columbia.edu,
 linux-arm-kernel@lists.infradead.org, liangpeng10@huawei.com,
 will@kernel.org, james.morse@arm.com, julien.thierry.kdev@gmail.com,
 suzuki.poulose@arm.com, kernel-team@android.com
X-SA-Exim-Mail-From: maz@kernel.org
X-SA-Exim-Scanned: No (on disco-boy.misterjones.org);
 SAEximRunCond expanded to false
Precedence: bulk
List-ID: <kvm.vger.kernel.org>
X-Mailing-List: kvm@vger.kernel.org

This small series addresses a couple of Spectre-v2 related issues:

- Fix a live migration regression introduced with the setting of CSV2
  on systems that are not affected by Spectre-v2, but that don't
  directly expose it in ID_AA64PFR0_EL1

- Inject an UNDEF exception if the guest tries to access any of
  SCXTNUM_ELx, as we don't advertise it to guests.

Patches on top of 5.10-rc2.

Marc Zyngier (3):
  KVM: arm64: Allow setting of ID_AA64PFR0_EL1.CSV2 from userspace
  KVM: arm64: Rename access_amu() to undef_access()
  KVM: arm64: Handle SCXTNUM_ELx traps

 arch/arm64/include/asm/kvm_host.h |  2 +
 arch/arm64/include/asm/sysreg.h   |  4 ++
 arch/arm64/kvm/arm.c              | 16 +++++++
 arch/arm64/kvm/sys_regs.c         | 70 +++++++++++++++++++++++--------
 4 files changed, 74 insertions(+), 18 deletions(-)
```
