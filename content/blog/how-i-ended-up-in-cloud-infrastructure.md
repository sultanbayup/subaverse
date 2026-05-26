---
title: "How I Ended Up in Cloud Infrastructure (And Why I'm Staying)"
date: '2025-05-25'
draft: false
tags: ['career', 'gcp', 'certifications', 'cloud', 'personal-growth', 'devops']
summary: "I studied physics, stumbled into tech, failed a cloud exam I had no business taking, and spent three years figuring out why I actually belong here."
featured_image: '/images/blog/how-i-ended-up-in-cloud-infrastructure/symbolic-of-a-career-decision.webp'
---

<figure>
  <img
    src="/images/blog/how-i-ended-up-in-cloud-infrastructure/symbolic-of-a-career-decision.webp"
    alt="A person standing at a crossroads, symbolic of a career decision"
  />
</figure>

I have a physics degree. I work in cloud infrastructure. These two facts don't obviously connect, and for a while I wasn't sure they ever would.

This is the story of how I got here — the wrong turns, the late nights, the exam I failed, and why I'm not switching to AI even though everyone keeps asking.

## The practical decision

When I was finishing college, I looked at the job market and made a call. Physics is interesting, but the employment options felt narrow. Tech, on the other hand, had real potential — locally and abroad. So I started learning.

I didn't jump straight into infrastructure. I started with data analytics and data science, because it felt like the closest bridge from physics. Both involve a lot of structured thinking, which felt familiar. It made sense at the time.

What I didn't expect was where that bridge would actually lead me.

## The internship that changed direction

My first role was an internship supporting a data engineer. The work was mostly operational — keeping pipelines running, helping with data workflows. But the environment touched both on-prem servers and GCP, and everything ran on Linux.

<figure>
  <img
    src="/images/blog/how-i-ended-up-in-cloud-infrastructure/first-contact-with-command-line.webp"
    alt="A terminal window open on a laptop, representing first contact with the command line"
  />
</figure>

That was my first real encounter with Linux. I installed Ubuntu on my personal laptop just to get comfortable with it outside of work hours. What started as a necessity turned into something I genuinely enjoyed. I kept going deeper than the job required.

About three months in, my manager sat down with me and asked where I wanted to go. That conversation turned into a full-time offer — not as a data engineer, but as an infrastructure engineer. Mostly ops work. I said yes.

## The exam I had no business taking

A few months into my full-time role, the company got an opportunity to send two people to a Google certification program. I was selected as one of the delegates.

I looked up the exam. The Professional Cloud Architect certification. Recommended experience: three years in the industry.

I had four months.

<figure>
  <img
    src="/images/blog/how-i-ended-up-in-cloud-infrastructure/overwhelming-challange-ahead.webp"
    alt="A person looking up at a mountain from the base, representing an overwhelming challenge ahead"
  />
</figure>

I registered anyway. Part stubbornness, part curiosity, part not fully understanding what I was getting into.

I failed. Honestly — what did I expect? I was a fresh graduate who had just started touching cloud infrastructure. But I knew exactly what I didn't know after that, and that's actually a useful place to be.

## Building the foundation properly

The following year, another certification opportunity came through. This time I was more honest with myself.

I wasn't ready for architect-level yet. I registered for the Associate Cloud Engineer exam instead — compute, networking, storage, IAM, basic Kubernetes. The fundamentals. It forced me to build a proper base rather than jumping straight to the advanced material.

I passed. About one year into my career. It was the first real external validation that I actually knew what I was doing, not just that I was surviving.

I told myself I'd come back for PCA eventually.

## The part nobody talks about: the actual work

Between the exams, there was just... the job. And the job was hard in ways that study guides don't prepare you for.

<figure>
  <img
    src="/images/blog/how-i-ended-up-in-cloud-infrastructure/late-night-incident-response.webp"
    alt="A dark desk setup late at night with monitor glow, representing late-night incident response"
  />
</figure>

One night, Prefect went down. The orchestration platform we used for data pipelines — just stopped. I started digging. The pods weren't running, and at that point I didn't really understand Kubernetes well enough to read the situation quickly. No AI to ask. Just me, the logs, and a lot of Stack Overflow.

Several hours in, I traced it back to Postgres. The database wasn't ready, so the pods wouldn't start. But when I tried to SSH into the VM to fix it, I couldn't log in. The disk was completely full.

So I stopped the VM, detached the disk, spun up a temporary VM, attached the disk there, and manually expanded it. Then reattached everything, brought the VM back up, and finally got into the database. The root cause: no log retention policy. A year's worth of logs sitting on disk, never cleaned up.

I deleted the old logs. Production came back up. I went to sleep.

The next morning I wrote the automation to handle log retention so it couldn't happen again.

That's the job. Nobody writes that in a study guide. You learn it at 2am when something is broken and you're the one who has to fix it.

Early career is uncomfortable. You're constantly in situations where you don't fully know what you're doing. But you figure it out, and then the next time it's a little less scary. That's how it works.

## Moving on, and finally getting PCA

I left that company for a better opportunity. The new role was at a company that was a Google Cloud partner, which meant access to the certification program every year.

I registered for PCA.

Three years into my career — which, as it turns out, is exactly what the exam recommends. The timing felt almost like a joke. I'd tried this exam when I had four months of experience. Now I had three years of actual infrastructure work behind me: managing GCP environments, dealing with real production issues, making architecture decisions under pressure.

I passed. It didn't feel like luck. It felt like the natural result of the work.

## DevOps wasn't a target — it was just what I was doing

Three months after PCA, I had a new goal: the Professional Cloud DevOps Engineer exam.

But here's the thing — it wasn't really a stretch. The new company was more product-focused than my previous one, which had been primarily a data company. There wasn't much app deployment there. Here, CI/CD was part of daily life. I was working with pipelines, deployment workflows, release processes. I was reading about SRE practices because they were directly relevant to what I was doing, not because I was studying for an exam.

The DevOps cert felt natural because the work had already taken me there. I passed.

## The AI question

At some point in the last couple of years, I started asking myself a question that I think a lot of engineers are quietly sitting with: should I pivot to AI?

It's a reasonable thing to wonder. AI is everywhere. The hype is real. And if you're early in your career, it's easy to feel like you're on the wrong track if you're not working on models or ML pipelines.

I thought about it seriously. And I decided to stay in infrastructure.

<figure>
  <img
    src="/images/blog/how-i-ended-up-in-cloud-infrastructure/data-center.webp"
    alt="A server rack in a data center corridor, representing the physical foundation that AI runs on"
  />
</figure>

My reasoning was simple: AI needs infrastructure to run. Every model, every training job, every inference endpoint — it all runs on compute, networking, storage, and the platforms that manage them. The skills I've built aren't becoming irrelevant. If anything, the demand for people who can run this stuff reliably is going up.

I also didn't want to restart. I've spent three years building real depth in cloud infrastructure and platform engineering. Throwing that away to start over in a field I'd be a beginner in again didn't make sense. I can learn enough about AI to work alongside it — and I already do, using it to move faster on the things I'm already good at.

I'm changing with it. But I'm not leaving.

## Where this goes

I'm not done with certifications, and I'm not done learning. Platform engineering, SRE, Kubernetes at scale — there's a lot of ground still to cover.

But more than the certs, what I care about is building things that actually work. Systems that don't fall over at 2am. Infrastructure that the people using it don't have to think about. That's the job.

I started as a physics student who made a practical bet on tech. Three years in, I think the bet paid off. Not because of the certifications — those are just markers. But because I found work that I'm genuinely good at and still want to get better at.

That's enough reason to stay.
