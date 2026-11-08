IA Automation - Nahuel Witt - Entrega Final - README

1. Caso de negocio con ROI justificado
Actualmente en un ecommerce se estan recibiendo casos de transacciones fallidas que deben ser revisadas una a una para determinar principalmente el motivo y posteriormente definir en función de un análisis manual, cual es la posible solución para ese caso especifico, si es que la hubiese.
Este proceso requiere de una inversión diaria de 1-2 horas (40 horas mensuales) solo para clasificar de manera ordenada todos los casos, quitando tiempo disponible de análisis, que es donde se encuentra el verdadero valor. Cómo está atado al tráfico y las transacciones del sitio, este proceso rutinario se ve impactado por el crecimiento del mismo durante eventos como HotSale, lo cual lo convierte en una tarea que no escala a largo plazo si no se automatiza.
A su vez, cada hora que ocupa el analista, tiene un costo de $9000 si nos basamos en un salario promedio de ~$1.600.000, por lo que estaríamos teniendo un costo asociado de aproximadamente $360.000 por mes, considerando las 40 horas mensuales.
Por otro lado, este modelo dejaría de tener sentido económico o técnico si el volumen de transacciones fallidas mensuales cayera por debajo de un umbral mínimo (por ejemplo, menos de 10 casos al mes), donde el costo de mantenimiento de las APIs, los nodos de n8n y la infraestructura superaría el ahorro de tiempo humano; o bien si la tasa de excepciones complejas no automatizables superará el 80%, haciendo que la intervención manual siga siendo inevitable. 


2. Al menos dos fuentes de entrada distintas

Debido a que el ecommerce es ficticio y no se cuenta con ordenes reales, para la simulación voy a usar la URL de prueba (Test URL) del propio nodo webhook y voy a disparar un evento ficticio enviándole un JSON desde Postman.
El json a utilizar en postman es el siguiente con el metodo POST:
{
  "batch_id": "BATCH-2026-11-06-01",
  "total_records": 40,
  "execution_timestamp": "2026-11-06T00:15:30Z",
  "environment": "production_hotsale",
  "transactions": [
    {
      "transaction_id": "TX-9001",
      "order_id": "ORD-7001",
      "amount": 185000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "4321",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-101",
        "name": "Sofia Gomez",
        "email": "sgomez@example.com",
        "phone": "+5491144556677",
        "device_ip": "190.19.110.12"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9002",
      "order_id": "ORD-7002",
      "amount": 42000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "8812",
        "installments": 1,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-102",
        "name": "Lucas Fernandez",
        "email": "lfernandez@example.com",
        "phone": "+5491133221100",
        "device_ip": "181.30.45.88"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9003",
      "order_id": "ORD-7003",
      "amount": 310000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "1190",
        "installments": 12,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-103",
        "name": "Martina Rossi",
        "email": "mrossi@example.com",
        "phone": "+5491155667788",
        "device_ip": "190.19.110.15"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9004",
      "order_id": "ORD-7004",
      "amount": 15000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "debit_card",
        "brand": "Visa",
        "last_four": "9931",
        "installments": 1,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-104",
        "name": "Joaquin Lopez",
        "email": "jlopez@example.com",
        "phone": "+5491122334455",
        "device_ip": "200.45.12.90"
      },
      "checkout_metadata": {
        "store_location": "Rosario",
        "is_events_promotion": false,
        "promo_code_applied": null
      }
    },
    {
      "transaction_id": "TX-9005",
      "order_id": "ORD-7005",
      "amount": 540000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "0041",
        "installments": 1,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_high_risk",
        "gateway_message": "Fraud engine score exceeded threshold",
        "http_code": 403
      },
      "customer": {
        "user_id": "USR-105",
        "name": "Unknown User",
        "email": "test_fraud@tempmail.com",
        "phone": "+5491100000000",
        "device_ip": "45.12.99.1"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9006",
      "order_id": "ORD-7006",
      "amount": 220000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "5521",
        "installments": 3,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-106",
        "name": "Camila Diaz",
        "email": "cdiaz@example.com",
        "phone": "+5491188990011",
        "device_ip": "190.19.110.40"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9007",
      "order_id": "ORD-7007",
      "amount": 95000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "3341",
        "installments": 6,
        "issuer_bank": "Macro"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-107",
        "name": "Mateo Romero",
        "email": "mromero@example.com",
        "phone": "+5491177665544",
        "device_ip": "181.15.20.10"
      },
      "checkout_metadata": {
        "store_location": "La Plata",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9008",
      "order_id": "ORD-7008",
      "amount": 13000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "1212",
        "installments": 1,
        "issuer_bank": "Naranja X"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_card_disabled",
        "gateway_message": "Card inactive or blocked by cardholder",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-108",
        "name": "Elena Alvarez",
        "email": "ealvarez@example.com",
        "phone": "+5491144332211",
        "device_ip": "190.200.10.5"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": false,
        "promo_code_applied": null
      }
    },
    {
      "transaction_id": "TX-9009",
      "order_id": "ORD-7009",
      "amount": 410000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "7789",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-109",
        "name": "Gonzalo Torres",
        "email": "gtorres@example.com",
        "phone": "+5491166778899",
        "device_ip": "190.19.110.88"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9010",
      "order_id": "ORD-7010",
      "amount": 68000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "9001",
        "installments": 3,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-110",
        "name": "Lucia Benitez",
        "email": "lbenitez@example.com",
        "phone": "+5491133445566",
        "device_ip": "200.16.88.3"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9011",
      "order_id": "ORD-7011",
      "amount": 175000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "4321",
        "installments": 3,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_call_for_authorize",
        "gateway_message": "Cardholder must call issuing bank to authorize transaction",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-111",
        "name": "Federico Silva",
        "email": "fsilva@example.com",
        "phone": "+5491155443322",
        "device_ip": "190.19.110.99"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9012",
      "order_id": "ORD-7012",
      "amount": 8900.0,
      "currency": "ARS",
      "payment_method": {
        "type": "debit_card",
        "brand": "Mastercard",
        "last_four": "1122",
        "installments": 1,
        "issuer_bank": "Mercado Pago"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_date",
        "gateway_message": "Expiration date invalid",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-112",
        "name": "Valentina Castro",
        "email": "vcastro@example.com",
        "phone": "+5491199887766",
        "device_ip": "181.44.12.33"
      },
      "checkout_metadata": {
        "store_location": "Santa Fe",
        "is_events_promotion": false,
        "promo_code_applied": null
      }
    },
    {
      "transaction_id": "TX-9013",
      "order_id": "ORD-7013",
      "amount": 295000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "6654",
        "installments": 6,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-113",
        "name": "Nicolas Molina",
        "email": "nmolina@example.com",
        "phone": "+5491122110099",
        "device_ip": "200.50.10.12"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9014",
      "order_id": "ORD-7014",
      "amount": 120000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "9812",
        "installments": 3,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-114",
        "name": "Agustina Ortiz",
        "email": "aortiz@example.com",
        "phone": "+5491144668800",
        "device_ip": "190.19.110.12"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9015",
      "order_id": "ORD-7015",
      "amount": 620000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "0041",
        "installments": 1,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_high_risk",
        "gateway_message": "Fraud engine score exceeded threshold",
        "http_code": 403
      },
      "customer": {
        "user_id": "USR-105",
        "name": "Unknown User",
        "email": "test_fraud@tempmail.com",
        "phone": "+5491100000000",
        "device_ip": "45.12.99.1"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9016",
      "order_id": "ORD-7016",
      "amount": 55000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "7711",
        "installments": 3,
        "issuer_bank": "Macro"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-115",
        "name": "Tomas Morales",
        "email": "tmorales@example.com",
        "phone": "+5491177889900",
        "device_ip": "181.22.90.1"
      },
      "checkout_metadata": {
        "store_location": "Tucumán",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9017",
      "order_id": "ORD-7017",
      "amount": 230000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "3344",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-116",
        "name": "Paula Suarez",
        "email": "psuarez@example.com",
        "phone": "+5491133557799",
        "device_ip": "190.19.110.45"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9018",
      "order_id": "ORD-7018",
      "amount": 18000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "debit_card",
        "brand": "Visa",
        "last_four": "9090",
        "installments": 1,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_other",
        "gateway_message": "Invalid cardholder name format",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-117",
        "name": "Bruno Mendez",
        "email": "bmendez@example.com",
        "phone": "+5491122446688",
        "device_ip": "200.10.44.1"
      },
      "checkout_metadata": {
        "store_location": "La Plata",
        "is_events_promotion": false,
        "promo_code_applied": null
      }
    },
    {
      "transaction_id": "TX-9019",
      "order_id": "ORD-7019",
      "amount": 340000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "1002",
        "installments": 12,
        "issuer_bank": "HSBC"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-118",
        "name": "Carla Rios",
        "email": "crios@example.com",
        "phone": "+5491111223344",
        "device_ip": "181.12.33.44"
      },
      "checkout_metadata": {
        "store_location": "Mar del Plata",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9020",
      "order_id": "ORD-7020",
      "amount": 124000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "5012",
        "installments": 3,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-120",
        "name": "Usuario 20",
        "email": "usuario20@example.com",
        "phone": "+5491128728463",
        "device_ip": "190.19.110.20"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9021",
      "order_id": "ORD-7021",
      "amount": 568000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "7912",
        "installments": 1,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_card_disabled",
        "gateway_message": "Card inactive or blocked by cardholder",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-121",
        "name": "Usuario 21",
        "email": "usuario21@example.com",
        "phone": "+5491113999315",
        "device_ip": "190.19.110.21"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9022",
      "order_id": "ORD-7022",
      "amount": 233000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "1434",
        "installments": 3,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_card_disabled",
        "gateway_message": "Card inactive or blocked by cardholder",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-122",
        "name": "Usuario 22",
        "email": "usuario22@example.com",
        "phone": "+5491197226012",
        "device_ip": "190.19.110.22"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9023",
      "order_id": "ORD-7023",
      "amount": 235000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "5557",
        "installments": 1,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_card_disabled",
        "gateway_message": "Card inactive or blocked by cardholder",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-123",
        "name": "Usuario 23",
        "email": "usuario23@example.com",
        "phone": "+5491131429110",
        "device_ip": "190.19.110.23"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9024",
      "order_id": "ORD-7024",
      "amount": 358000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "4527",
        "installments": 6,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-124",
        "name": "Usuario 24",
        "email": "usuario24@example.com",
        "phone": "+5491123718431",
        "device_ip": "190.19.110.24"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9025",
      "order_id": "ORD-7025",
      "amount": 399000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "6635",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-125",
        "name": "Usuario 25",
        "email": "usuario25@example.com",
        "phone": "+5491115831819",
        "device_ip": "190.19.110.25"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9026",
      "order_id": "ORD-7026",
      "amount": 559000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "2291",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_high_risk",
        "gateway_message": "Fraud engine score exceeded threshold",
        "http_code": 403
      },
      "customer": {
        "user_id": "USR-126",
        "name": "Usuario 26",
        "email": "usuario26@example.com",
        "phone": "+5491194374605",
        "device_ip": "190.19.110.26"
      },
      "checkout_metadata": {
        "store_location": "Rosario",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9027",
      "order_id": "ORD-7027",
      "amount": 206000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "4733",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-127",
        "name": "Usuario 27",
        "email": "usuario27@example.com",
        "phone": "+5491120709497",
        "device_ip": "190.19.110.27"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9028",
      "order_id": "ORD-7028",
      "amount": 113000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "8428",
        "installments": 6,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-128",
        "name": "Usuario 28",
        "email": "usuario28@example.com",
        "phone": "+5491131831063",
        "device_ip": "190.19.110.28"
      },
      "checkout_metadata": {
        "store_location": "Rosario",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9029",
      "order_id": "ORD-7029",
      "amount": 373000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "2169",
        "installments": 3,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-129",
        "name": "Usuario 29",
        "email": "usuario29@example.com",
        "phone": "+5491181691040",
        "device_ip": "190.19.110.29"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9030",
      "order_id": "ORD-7030",
      "amount": 177000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "5422",
        "installments": 3,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_high_risk",
        "gateway_message": "Fraud engine score exceeded threshold",
        "http_code": 403
      },
      "customer": {
        "user_id": "USR-130",
        "name": "Usuario 30",
        "email": "usuario30@example.com",
        "phone": "+5491153524491",
        "device_ip": "190.19.110.30"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9031",
      "order_id": "ORD-7031",
      "amount": 244000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "7572",
        "installments": 6,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-131",
        "name": "Usuario 31",
        "email": "usuario31@example.com",
        "phone": "+5491118883684",
        "device_ip": "190.19.110.31"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9032",
      "order_id": "ORD-7032",
      "amount": 590000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "9179",
        "installments": 12,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-132",
        "name": "Usuario 32",
        "email": "usuario32@example.com",
        "phone": "+5491196282117",
        "device_ip": "190.19.110.32"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9033",
      "order_id": "ORD-7033",
      "amount": 156000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "5040",
        "installments": 6,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-133",
        "name": "Usuario 33",
        "email": "usuario33@example.com",
        "phone": "+5491188461803",
        "device_ip": "190.19.110.33"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9034",
      "order_id": "ORD-7034",
      "amount": 418000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "3266",
        "installments": 12,
        "issuer_bank": "BBVA"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-134",
        "name": "Usuario 34",
        "email": "usuario34@example.com",
        "phone": "+5491122201654",
        "device_ip": "190.19.110.34"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9035",
      "order_id": "ORD-7035",
      "amount": 122000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "7916",
        "installments": 1,
        "issuer_bank": "Santander"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-135",
        "name": "Usuario 35",
        "email": "usuario35@example.com",
        "phone": "+5491161642594",
        "device_ip": "190.19.110.35"
      },
      "checkout_metadata": {
        "store_location": "Mendoza",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9036",
      "order_id": "ORD-7036",
      "amount": 489000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "1188",
        "installments": 1,
        "issuer_bank": "Macro"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-136",
        "name": "Usuario 36",
        "email": "usuario36@example.com",
        "phone": "+5491182070937",
        "device_ip": "190.19.110.36"
      },
      "checkout_metadata": {
        "store_location": "Rosario",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9037",
      "order_id": "ORD-7037",
      "amount": 358000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Visa",
        "last_four": "8123",
        "installments": 3,
        "issuer_bank": "Banco Galicia"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_bad_filled_security_code",
        "gateway_message": "Invalid CVV code",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-137",
        "name": "Usuario 37",
        "email": "usuario37@example.com",
        "phone": "+5491170897765",
        "device_ip": "190.19.110.37"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9038",
      "order_id": "ORD-7038",
      "amount": 279000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "9317",
        "installments": 1,
        "issuer_bank": "Macro"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-138",
        "name": "Usuario 38",
        "email": "usuario38@example.com",
        "phone": "+5491193926371",
        "device_ip": "190.19.110.38"
      },
      "checkout_metadata": {
        "store_location": "Rosario",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9039",
      "order_id": "ORD-7039",
      "amount": 529000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Mastercard",
        "last_four": "3504",
        "installments": 6,
        "issuer_bank": "Macro"
      },
      "gateway_response": {
        "status": "error",
        "error_code": "gateway_timeout",
        "gateway_message": "Acquirer timeout during authorization",
        "http_code": 504
      },
      "customer": {
        "user_id": "USR-139",
        "name": "Usuario 39",
        "email": "usuario39@example.com",
        "phone": "+5491131682744",
        "device_ip": "190.19.110.39"
      },
      "checkout_metadata": {
        "store_location": "CABA",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    },
    {
      "transaction_id": "TX-9040",
      "order_id": "ORD-7040",
      "amount": 341000.0,
      "currency": "ARS",
      "payment_method": {
        "type": "credit_card",
        "brand": "Amex",
        "last_four": "2832",
        "installments": 6,
        "issuer_bank": "American Express"
      },
      "gateway_response": {
        "status": "rejected",
        "error_code": "cc_rejected_insufficient_amount",
        "gateway_message": "Insufficient funds in card account",
        "http_code": 400
      },
      "customer": {
        "user_id": "USR-140",
        "name": "Usuario 40",
        "email": "usuario40@example.com",
        "phone": "+5491151273847",
        "device_ip": "190.19.110.40"
      },
      "checkout_metadata": {
        "store_location": "Córdoba",
        "is_events_promotion": true,
        "promo_code_applied": "HOTSALE2026"
      }
    }
  ]
}


4. Un AI Agent (no un nodo de IA simple)

En este caso, se decidió utilizar un Agente IA y no un nodo Basic LLM Chain, principalmente porque se requiere que el agente tenga un razonamiento dinámico, uso de herramientas externas (RAG) y toma de decisiones de manera autónoma para poder devolver una respuesta coherente que le permita al analista tener un panorama claro sobre lo sucedido con cada transacción.
En resumen el Agente actúa como un analista autónomo capaz de investigar, razonar y estructurar una solución técnica precisa para cada caso.


5. Decisión de modelo: local vs. nube

Para este workflow en particular opte por un modelo 100% en la nube. Esta decisión se justifica principalmente por el costo según el volumen esperado, ya que para etapas iniciales el gasto de infraestructura es nulo al aprovechar los niveles gratuitos, evitando así la inversión en servidores dedicados con GPU. En términos de privacidad, los proveedores en la nube de nivel empresarial garantizan políticas estrictas de no retención de datos para proteger la información transaccional sensible de los clientes. Finalmente, en cuanto a independencia y rendimiento, la nube ofrece alta velocidad de procesamiento y modelos hiperoptimizados para respuestas estructuradas en JSON, aunque requiere gestionar los límites de cuota (rate limits) mediante pausas en el flujo o escalando a planes pagos a medida que crezca el volumen. 

7. Manejo de errores y casos límite
Para el manejo de errores y casos límite, el flujo contempla dos puntos críticos donde la automatización podría fallar: la interrupción de la API de IA por saturación y la generación de datos ambiguos o mal formateados por parte del agente. Por un lado, la caída temporal o el bloqueo por límites de cuota de la API de Groq se previene y mitiga mediante la incorporación de un nodo de retardo (Wait) de 15 segundos entre iteraciones del bucle, sumado a la activación de la política de reintentos automáticos (Retry On Fail) en la configuración del nodo, asegurando que un error HTTP 429 no aborte la ejecución de las demás transacciones pendientes. Por otro lado, el riesgo de que la IA genere respuestas inconsistentes o en texto libre conversacional que rompan la base de datos se resuelve aplicando un contrato de validación estricta a través del nodo Structured Output Parser, el cual valida la estructura JSON antes de ejecutar cualquier acción de escritura en Google Sheets para garantizar que únicamente se inserten diagnósticos limpios y bien formateados en la planilla final.

