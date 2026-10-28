# Assignment-3
# payment_processor.py
from abc import ABC, abstractmethod
from datetime import datetime
import functools
import uuid


# ---------- Receipt ----------
class Receipt:
    def __init__(self, amount, method, status):
        self.txn_id = uuid.uuid4().hex[:8].upper()
        self.amount = amount
        self.method = method
        self.status = status
        self.timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")

    def __str__(self):
        return (f"Receipt[{self.txn_id}] {self.method} | "
                f"Rs.{self.amount} | {self.status} | {self.timestamp}")

    def __repr__(self):
        return (f"Receipt(txn_id={self.txn_id!r}, amount={self.amount}, "
                f"method={self.method!r}, status={self.status!r})")


# ---------- Strategy interface ----------
class PaymentStrategy(ABC):
    name = "Generic Payment"

    @abstractmethod
    def validate(self):
        ...

    @abstractmethod
    def pay(self, amount):
        ...

    def _make_receipt(self, amount, status):
        return Receipt(amount, self.name, status)


# ---------- Concrete strategies ----------
class CreditCardPayment(PaymentStrategy):
    name = "Credit Card"

    def __init__(self, card_number, cvv, expiry):
        self.card_number = card_number
        self.cvv = cvv
        self.expiry = expiry

    def validate(self):
        return (self.card_number.isdigit() and len(self.card_number) == 16
                and self.cvv.isdigit() and len(self.cvv) == 3)

    def pay(self, amount):
        if not self.validate():
            return self._make_receipt(amount, "FAILED")
        print(f"  Charging card ending {self.card_number[-4:]}...")
        return self._make_receipt(amount, "SUCCESS")


class PayPalPayment(PaymentStrategy):
    name = "PayPal"

    def __init__(self, email, password):
        self.email = email
        self.password = password

    de
