---
title: Мониторинг служб контейнерных приложений
description: Сведения об основных аспектах, связанных с мониторингом контейнерных архитектур
ms.date: 02/15/2019
ms.openlocfilehash: e41df53ad94784436442c3cf7defed3fab510455
ms.sourcegitcommit: e7748001b1cee80ced691d8a76ca814c0b02dd9b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/14/2020
ms.locfileid: "86374446"
---
# <a name="monitor-containerized-application-services"></a>Мониторинг служб контейнерных приложений

Для эффективного мониторинга и анализа поведения приложения в целом важно разбить его на несколько контейнеров и микрослужб.

## <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](https://azure.microsoft.com/services/monitor/) — это расширяемая служба аналитики, которая отслеживает работающие приложения. С ее помощью можно обнаруживать и диагностировать проблемы производительности, а также понять, что пользователи в действительности делают с вашим приложением. Эта служба ориентирована на разработчиков и предназначена для постоянного улучшения производительности и удобства ваших приложений и служб. Azure Monitor работает с обычными или веб-службами и автономными приложениями на самых разных платформах, включая .NET, Java, Node.js и многие другие, которые могут размещаться как локально, так и в облаке.

### <a name="additional-resources"></a>Дополнительные ресурсы

- **Обзор Azure Monitor** \
  <https://docs.microsoft.com/azure/azure-monitor/overview>

- **Что такое Azure Application Insights?** \
  <https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview>

- **Что такое метрики Azure Monitor?** \
  <https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics>

- **Решение для мониторинга контейнеров в Azure Monitor** \
  <https://docs.microsoft.com/azure/azure-monitor/insights/containers>

## <a name="security-and-backup-services"></a>Службы безопасности и резервного копирования

Чтобы обеспечить эффективную поддержку приложений и инфраструктуры в соответствии с постоянно изменяющимися бизнес-требованиями, необходимо выполнять множество самых разных рутинных операций и обрабатывать большие объемы сведений. В среде микрослужб ситуация становится еще сложнее, поскольку для выполнения любых действий вам требуется и высокоуровневое, и детализированное представление.

В Azure представлены средства управления, позволяющие получить унифицированное представление по четырем ключевым аспектам ваших облачных и локальных ресурсов:

- **Безопасность**. Используйте [Центр безопасности Azure](https://azure.microsoft.com/services/security-center/).
  - Полная прозрачность и эффективное управление безопасностью виртуальных машин, приложений и рабочих нагрузок.
  - Централизованное управление политиками безопасности и интеграция с существующими процессами и средствами.
  - Обнаружение реальных угроз благодаря расширенной аналитике.

- **Резервное копирование**. Используйте [Azure Backup](https://azure.microsoft.com/services/backup/).
  - Исключение дорогостоящих перебоев в работе, обеспечение соответствия требованиям и защита данных от программ-шантажистов и человеческих ошибок.
  - Шифрование данных при передаче и во время хранения.
  - Защита от несанкционированного доступа на основе многофакторной проверки подлинности.

- **Локальные ресурсы**. Используйте [гибридные облачные решения](https://azure.microsoft.com/solutions/hybrid-cloud-app/).

>[!div class="step-by-step"]
>[Назад](manage-production-docker-environments.md)
>[Вперед](../key-takeaways/index.md)
