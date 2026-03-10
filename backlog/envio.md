# Enviar notificações automáticas

Podemos enviar o link do relatório do dashboard via **Slack, Teams, Discord, e-mail ou qualquer outro webhook**.

---

## 🔹 Como funcionaria

1. **Gerar o link do dashboard** no final do job `deploy`:

```bash id="7n7z9b"
REPORT_URL="https://<SEU-USER>.github.io/postman-newman/$DATE/"
```

2. **Enviar uma mensagem via webhook** (Slack, Discord etc.):

### Exemplo para Slack

```yaml id="slack-webhook"
- name: Notify Slack
  if: always() # garante envio mesmo se alguns testes falharem
  uses: 8398a7/action-slack@v3
  with:
    status: custom
    fields: repo,message
    custom_payload: |
      {
        "text": "🧪 Novo relatório de testes disponível: <$REPORT_URL|Clique aqui> \nStatus: ${{ needs.test.outputs.status }}"
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

> ✅ Você precisaria apenas criar o **secret** `SLACK_WEBHOOK_URL` no seu repositório.

---

### Exemplo para e-mail via GitHub Actions

Você pode usar a action [dawidd6/action-send-mail](https://github.com/dawidd6/action-send-mail):

```yaml id="email-webhook"
- name: Send email notification
  uses: dawidd6/action-send-mail@v3
  with:
    server_address: smtp.gmail.com
    server_port: 465
    username: ${{ secrets.EMAIL_USER }}
    password: ${{ secrets.EMAIL_PASS }}
    subject: "Novo relatório de testes disponível"
    body: "O relatório diário da API está disponível: $REPORT_URL"
    to: "destinatario@exemplo.com"
    from: "QA Bot <noreply@exemplo.com>"
```

---

## 🔹 Benefícios

* Toda execução gera **notificação automática** com link direto para o relatório.
* Permite **monitoramento remoto** das execuções da API.
* Pode combinar com **status PASS/FAIL**, número de testes/falhas, etc.

---

Se você quiser, posso te enviar **uma versão do workflow completa**, já **com envio automático do link do relatório para Slack**, usando secrets, pronto para colocar em produção.

Quer que eu faça isso?
